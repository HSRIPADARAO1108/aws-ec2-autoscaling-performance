# AWS EC2 Auto Scaling & CloudWatch Performance Lab

## 📌 Project Overview
This project demonstrates the implementation of a **highly available and self-healing infrastructure** using AWS. I configured an Auto Scaling Group (ASG) to respond to real-time CPU stress, proving the system's ability to "Scale-Out" during traffic spikes and "Scale-In" to optimize costs during idle periods.


---

## 🛠 Tech Stack
* **Cloud Provider:** AWS (EC2, ASG, CloudWatch)
* **Operating System:** Ubuntu 24.04 LTS
* **Monitoring & Stress Tools:** `stress-ng`, `htop`

---

## 🚀 Execution & Phase Explanation

### Phase 1: High-Intensity Load Generation
* **Action:** Executed `stress-ng --cpu 2 --timeout 300s` to saturate instance resources.
* **Technical Proof:** The terminal confirms a successful 5-minute stress run, and the `htop` dashboard shows both CPU cores pinned at **100.0%** utilization.

![Stress-ng Execution](./Screenshot%202026-01-14%20003455.png)
![CPU Saturation Proof](./Screenshot%202026-01-14%20003551.png)

### Phase 2: Automated Alarm Triggering
* **Action:** CloudWatch Alarms monitored the CPU metrics against a 50% threshold.
* **Technical Proof:** The CloudWatch dashboard confirms that the **AlarmHigh** transitioned to the **"In alarm"** state once the threshold was breached.

![CloudWatch Alarm Triggered](./Screenshot%202026-01-14%20012114.png)

### Phase 3: Automated Scaling & Recovery
* **Action:** The ASG responded by launching new instances to handle the load and subsequently terminated them once the stress test concluded.

**Scale-Out (Fleet Expansion):**
The EC2 dashboard shows the fleet successfully expanded to 8 instances to meet demand.
![EC2 Fleet Expansion](./Screenshot%202026-01-14%20010040.png)

**Scale-In (Automatic Cost Optimization):**
The ASG Activity History proves the system's "self-healing" nature by automatically terminating extra instances to return to the minimum desired capacity.
![ASG Termination Logs](./Screenshot%202026-01-14%20010201.png)

---

## 🧠 Lessons Learnt & Critical Insights

### 1. Metric Resolution vs. Response Time
During the lab, I observed a 15-minute delay before scaling occurred.
* **The Insight:** Using **Basic Monitoring** (5-minute intervals) requires three data points (15 minutes total) to trigger a scaling event. 
* **Professional Recommendation:** In production, I would enable **Detailed Monitoring** (1-minute intervals) to reduce the response time to 3 minutes.

### 2. Infrastructure Resilience (Decoupling)
My local SSH connection reset during the test, but the scaling process was unaffected.
* **The Insight:** This confirms that AWS automation is **decoupled** from the management terminal; the infrastructure logic lives securely in the AWS control plane.

### 3. Efficiency via Target Tracking
I successfully used **Target Tracking Policies** to automate cost management. The system proved its value by terminating "zombie" resources as soon as CPU utilization dropped below 35%.

---

## 🧹 Cleanup
To maintain cost discipline, the following were deleted after project completion:
1.  **Auto Scaling Group** (this automatically terminated all running instances).
2.  **Launch Template** and **CloudWatch Alarms**.
