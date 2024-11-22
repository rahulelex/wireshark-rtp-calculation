# wireshark-rtp-calculation
This repository provides formulas to calculate jitter, skew and delta.

Calculation Details
1. Nominal Time Difference Calculation:
The nominal time difference is calculated using the formula:

```sh
clock_rate= 16000
nominaltime_diff = extended_timestamp - prev_extended_timestamp
nominaltime_diff = nominaltime_diff / (clock_rate / 1000)
```
This represents the time difference between two extended timestamps, normalized by the clock rate (in milliseconds).

2. Expected Time Calculation:
Using the nominal time difference, the expected time for the current iteration is calculated:
```sh
expected_time = previous_time + nominaltime_diff
```
3. Current Time Difference:
The difference between the current_time and the expected_time is calculated and its absolute value is taken to determine the current discrepancy:
```sh
current_diff = abs(current_time - expected_time)
```

4. Jitter Calculation:
Jitter is calculated as a weighted average of the previous jitter value and the current time difference. The weight given to the previous jitter is 15/16, while the current difference gets a weight of 1/16:
```sh
jitter = round(((15 * previous_jitter) + current_diff) / 16, 6)
```
This smooths out fluctuations in the timing, producing a more stable estimate of jitter.

5. Time Delta:
The difference between current_time and previous_time is calculated and rounded to the nearest integer to get the delta in milliseconds:
```sh
delta = round(current_time - previous_time)
```
6. Nominal Time Update:
The nominal time is updated based on the previous nominal time and the nominal time difference:
```sh
nominal_time = prev_nominal_time + nominaltime_diff
```
7. Arrival Time Update:
The arrival time is updated by adding the calculated delta to the previous arrival time:
```sh
arrival_time = prev_arrival_time + delta
```
8. Skew Calculation:
Skew is calculated as the difference between the nominal time and the arrival time:
```sh
skew = nominal_time - arrival_time
```
The absolute value of skew is then calculated:
```sh
abs_skew = abs(skew)
```
9. Max Skew Update:
If the absolute skew exceeds the previous maximum skew, the maximum skew is updated:
```sh
if abs_skew > abs(max_skew):
    max_skew = skew
```
10. Previous Values Update:
Finally, the previous values are updated for the next iteration:
```sh
prev_extended_timestamp = extended_timestamp
previous_time = current_time
previous_jitter = jitter
prev_nominal_time = nominal_time
prev_arrival_time = arrival_time
```
