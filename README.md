# matlab-data-segmentation
hi i want your help to make a code for segmenting a real time data. i need to know a real time tested data's upper and lower segments of one wave,  calculate distance, frequency, decimation of 3 to 5 waves in a data
% Assuming 'data' is your real-time signal, and 'time' is the corresponding time vector
% data: The raw sensor data (real-time signal)
% time: The corresponding time vector

% Define parameters (you may need to adjust these)
threshold = 0.1; % Threshold for detecting cycle boundaries (can be adjusted)
sampling_rate = 1000; % Sampling rate in Hz (for example, 1000 samples per second)
cycle_length = 0; % Variable to store cycle lengths, if necessary
decimation_factor = 5; % Factor by which to downsample data (decimation)

% Example: Detect zero-crossings to segment cycles
% Zero-crossing detection is just an example; you can use a more sophisticated approach based on your data

cycle_start_indices = []; % Indices where cycles start
cycle_end_indices = []; % Indices where cycles end
cycle_frequencies = []; % Store frequencies of each cycle
cycle_distances = []; % Store distance (time duration or other measures) for each cycle
cycles = {}; % Cell array to store segmented cycles

% Loop through the data to find zero-crossings and calculate cycle properties
for i = 2:length(data)
    if data(i-1) <= 0 && data(i) > 0 % Upward crossing (change based on your data characteristics)
        cycle_start_indices = [cycle_start_indices, i];
    elseif data(i-1) >= 0 && data(i) < 0 % Downward crossing (change based on your data characteristics)
        cycle_end_indices = [cycle_end_indices, i];
    end
end

% Ensure that cycles are correctly paired (start and end)
if length(cycle_start_indices) > length(cycle_end_indices)
    cycle_end_indices = [cycle_end_indices, length(data)]; % For the last cycle
end

% Segment data into individual cycles and compute properties
for i = 1:length(cycle_start_indices)
    start_idx = cycle_start_indices(i);
    end_idx = cycle_end_indices(i);
    
    % Extract the cycle segment
    cycle_data = data(start_idx:end_idx);
    cycles{i} = cycle_data; % Store the cycle in the cell array
    
    % Time duration of the cycle (distance in time)
    cycle_time_duration = time(end_idx) - time(start_idx);
    cycle_distances = [cycle_distances, cycle_time_duration];
    
    % Frequency calculation (1 / cycle_time_duration)
    if cycle_time_duration > 0
        cycle_frequency = 1 / cycle_time_duration;
        cycle_frequencies = [cycle_frequencies, cycle_frequency];
    else
        cycle_frequencies = [cycle_frequencies, NaN]; % If duration is zero (error case)
    end
    
    % Decimate the cycle data (downsampling)
    decimated_cycle_data = downsample(cycle_data, decimation_factor);
    % Store the decimated cycle data (optional, for further analysis)
    cycles{i} = decimated_cycle_data;
end

% Plot the segmented cycles (optional)
figure;
hold on;
for i = 1:length(cycles)
    plot(time(cycle_start_indices(i):cycle_end_indices(i)), cycles{i});
end
title('Segmented Cycles with Decimation');
xlabel('Time (seconds)');
ylabel('Signal Value');
hold off;

% Output cycle information
fprintf('Cycle Information:\n');
for i = 1:length(cycles)
    fprintf('Cycle %d:\n', i);
    fprintf('  Duration: %.3f seconds\n', cycle_distances(i));
    fprintf('  Frequency: %.3f Hz\n', cycle_frequencies(i));
    fprintf('  Decimated Cycle Length: %d points\n\n', length(cycles{i}));
end
