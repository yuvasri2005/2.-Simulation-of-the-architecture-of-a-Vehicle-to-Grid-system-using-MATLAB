# Simulation-of-the-architecture-of-a-Vehicle-to-Grid-system-using-MATLAB
## AIM
To Simulate the architecture of a Vehicle-to-Grid system using MATLAB 

## APPARATUS REQUIRED
•	MATLAB

## MATLAB CODING

clc;

clear;

close all;

%% Simulation Parameters

Ts = 1; % Time step in seconds

T_total = 3600; % Total simulation time (1 hour)

n = T_total / Ts; % Number of time steps

time = (0:n-1) * Ts / 60; % Time in minutes

%% EV Battery Parameters

battery_capacity_kWh = 50;

SOC_init = 0.6; % 60% initial SOC

SOC = SOC_init * battery_capacity_kWh;

SOC_min = 0.2 * battery_capacity_kWh;

SOC_max = 0.9 * battery_capacity_kWh;

V_batt = 400; % Battery nominal voltage

%% Converter Efficiency

charging_eff = 0.95;

discharging_eff = 0.90;

%% Charging and Discharging Power Limits

charging_power = 10; % kW

discharging_power = 8; % kW

%% Grid Demand Profile (Synthetic)

base_load = 4; % kW

load_variation = 3 * sin(2 * pi * (0:n-1)/n); % daily profile

noise = randn(1, n); % randomness

grid_demand = base_load + load_variation + 0.5 * noise; % total demand

%% Thresholds for action

high_demand_threshold = 6; % kW

low_demand_threshold = 3; % kW

%% Preallocation

SOC_history = zeros(1, n);

power_flow = zeros(1, n); % EV power (+ for charging, - for discharging)

grid_power = zeros(1, n); % Net grid power after EV compensation

%% Simulation Loop

for t = 1:n

 if grid_demand(t) > high_demand_threshold && SOC > SOC_min
 
 % Discharging mode (V2G)
 
 P_out = min(discharging_power, SOC - SOC_min);
 
 P_out = P_out * discharging_eff; % apply discharging loss
 
 power_flow(t) = -P_out;
 
 SOC = SOC - P_out * Ts / 3600; % Update SOC in kWh
 
 elseif grid_demand(t) < low_demand_threshold && SOC < SOC_max
 
 % Charging mode (G2V)
 
 P_in = min(charging_power, SOC_max - SOC);
 
 P_in = P_in / charging_eff; % apply charging loss
 
 power_flow(t) = P_in;
 
 SOC = SOC + P_in * Ts / 3600; % Update SOC
 
 else
 
 power_flow(t) = 0;
 
 end

 SOC_history(t) = SOC;
 
 grid_power(t) = grid_demand(t) + power_flow(t); % Net grid demand
 
end

%% Normalized SOC

SOC_norm = SOC_history / battery_capacity_kWh;

%% Plotting

figure;

subplot(4,1,1);

plot(time, grid_demand, 'k', 'LineWidth', 1.5);

ylabel('Grid Load (kW)');

title('Grid Demand Profile'); grid on;

subplot(4,1,2);

plot(time, power_flow, 'b', 'LineWidth', 1.5);

ylabel('EV Power Flow (kW)');

title('EV Power Exchange (+G2V, -V2G)'); grid on;

subplot(4,1,3);

plot(time, SOC_norm, 'r', 'LineWidth', 1.5);

ylabel('SOC (0–1)');

title('State of Charge (SOC)'); ylim([0 1]); grid on;

subplot(4,1,4);

plot(time, grid_power, 'm', 'LineWidth', 1.5);

xlabel('Time (minutes)');

ylabel('Net Grid Power (kW)');

title('Grid Power After V2G Compensation'); grid on;

## Output

<img width="1600" height="845" alt="image" src="https://github.com/user-attachments/assets/8bb1e10e-d36b-42e5-aac1-bf317d687342" />

## Result
The MATLAB simulation of the Vehicle-to-Grid (V2G) architecture successfully demonstrated.
