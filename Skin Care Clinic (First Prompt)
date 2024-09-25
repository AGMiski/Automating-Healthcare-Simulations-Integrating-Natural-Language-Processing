import simpy
import numpy as np
import matplotlib.pyplot as plt

# Simulation parameters (all in minutes)
MEAN_INTERARRIVAL_TIME = 16.2
TREATMENT_TIME_MIN = 23
TREATMENT_TIME_MODE = 25
TREATMENT_TIME_MAX = 27
SIMULATION_HOURS_PER_DAY = 12
SIMULATION_DAYS = 14
REPLICATIONS = 100

# Simulation results
num_patients_exited = []
average_time_in_queue = []

def patient(env, name, doctors, queue_times):
    arrival_time = env.now
    with doctors.request() as request:
        yield request
        queue_time = env.now - arrival_time
        queue_times.append(queue_time)
        treatment_time = np.random.triangular(TREATMENT_TIME_MIN, TREATMENT_TIME_MODE, TREATMENT_TIME_MAX)
        yield env.timeout(treatment_time)

def run_clinic(env, doctors, interarrival_time, queue_times):
    i = 0
    while True:
        yield env.timeout(np.random.exponential(interarrival_time))
        env.process(patient(env, f'Patient {i}', doctors, queue_times))
        i += 1

for _ in range(REPLICATIONS):
    env = simpy.Environment()
    doctors = simpy.Resource(env, capacity=2)
    queue_times = []

    env.process(run_clinic(env, doctors, MEAN_INTERARRIVAL_TIME, queue_times))
    env.run(until=SIMULATION_HOURS_PER_DAY * 60 * SIMULATION_DAYS)

    num_patients_exited.append(len(queue_times))
    average_time_in_queue.append(np.mean(queue_times))

total_patients_exited = np.mean(num_patients_exited)
avg_time_in_queue = np.mean(average_time_in_queue)

# Print results
print(f"Total patients exited: {total_patients_exited}")
print(f"Average time in queue: {avg_time_in_queue:.2f} minutes")

# Plotting the average time in queue
plt.figure(figsize=(10, 6))
plt.plot(range(REPLICATIONS), average_time_in_queue, marker='o', linestyle='-')
plt.title('Average Time in Queue per Replication')
plt.xlabel('Replication')
plt.ylabel('Average Time in Queue (minutes)')
plt.grid(True)
plt.show()

