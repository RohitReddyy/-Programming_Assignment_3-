
# Programming Assignment 3 – Pantheon Congestion Control Evaluation

This repository contains all the configuration files, scripts, and results for evaluating congestion control algorithms using the Pantheon framework.

## Requirements

- OS: Ubuntu 20.04.6 (recommended in VirtualBox or native Linux)
- Python: 2.7.18 (via virtualenv)
- Tools & Dependencies:
  - `pip2`, `matplotlib`, `numpy`, `pyyaml`, `tabulate`
  - `Mahimahi`
  - `iperf`
  - `texlive-latex-recommended`
  - Pantheon dependencies (`tools/install_deps.sh`)

## Setup Instructions

```bash
# 1. Clone this repo
git clone https://github.com/RohitReddyy/-Programming_Assignment_3-.git
cd -Programming_Assignment_3-

# 2. Set up Python 2.7 virtual environment
sudo apt install virtualenv
virtualenv -p /usr/bin/python2 pantheon-env
source pantheon-env/bin/activate

# 3. Install pip2 and dependencies
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
python2 get-pip.py
pip2 install matplotlib numpy tabulate pyyaml

# 4. Install system packages
sudo apt install mahimahi iperf texlive-latex-recommended

# 5. Clone Pantheon and initialize
git clone https://github.com/StanfordSNR/pantheon.git
cd pantheon
tools/install_deps.sh
git submodule update --init --recursive

# 6. Build pantheon tunnel
cd third_party/pantheon-tunnel
chmod +x autogen.sh
./autogen.sh
./configure
make
sudo make install

# 7. Prepare schemes
cd ~/pantheon/src/experiments
python2 setup.py --setup --schemes "cubic bbr vegas"
```

## Running Experiments 
```bash
# Run on high-bandwidth profile
PYTHONPATH=src python2 test.py local --schemes "cubic bbr vegas" \
  --data-dir test_50mbps_10ms \
  --uplink-trace traces/50mbps.trace \
  --downlink-trace traces/50mbps.trace

# Run on constrained-bandwidth profile
PYTHONPATH=src python2 test.py local --schemes "cubic bbr vegas" \
  --data-dir test_1mbps_200ms \
  --uplink-trace traces/1mbps.trace \
  --downlink-trace traces/1mbps.trace
```

## Analyzing Results
```bash
# Generate throughput/delay/loss graphs
cd ~/pantheon
python2 src/analysis/analyze.py --data-dir test_50mbps_10ms
python2 src/analysis/analyze.py --data-dir test_1mbps_200ms
```

## Output
All result graphs (throughput, delay, loss rate) and data logs are stored in:
```bash
pantheon/src/experiments/rohitfiles_50mbps_10ms/
pantheon/src/experiments/rohitfiles_1mbps_200ms/
```
You can also use the provided Python scripts in this repo to generate combined plots (e.g., RTT vs. Throughput).

## Directory Structure
-   `/results`: Contains all PNGs and data extracted
    
-   `/scripts`: Custom plotting scripts
    
-   `/pantheon`: Submodule with experiment engine
    
-   `report.pdf`: Final report with graphs and analysis

## Conclusion

This project provided valuable insights into how different congestion control algorithms behave under varying network conditions. Through detailed testing using the Pantheon framework, it became clear that Cubic is highly effective in high-bandwidth environments, delivering the best throughput performance but at the cost of higher latency and packet loss in constrained networks. BBR, on the other hand, consistently maintained lower delays across both test scenarios, making it a strong choice for latency-sensitive applications, though it sacrifices some throughput. Vegas offered a balanced approach, with moderate throughput and the lowest loss rates, but struggled to maintain low delays under stress. 

Ultimately, the choice of the "best" algorithm depends on application requirements: Cubic for throughput-heavy tasks, BBR for real-time responsiveness, and Vegas for environments where fairness and stability matter most.
