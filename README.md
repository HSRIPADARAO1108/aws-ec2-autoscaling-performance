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
* **Action:** Executed `stress-ng --cpu 2 --timeout 900s` to saturate instance resources.
* **Evidence:** /Screenshot 2026-01-14 003551.png shows both CPU cores pinned at **100.0%** utilization.
* **Logic:** Manual stress injection is used to validate that CloudWatch correctly monitors and reports threshold breaches.

### Phase 2: Automated Alarm Triggering
* **Action:** CloudWatch Alarms monitored the CPU metrics against a 50% threshold.
* **Evidence:** `image_8bc8aa.png` captures the transition of monitoring states. 
* **Logic:** The **AlarmHigh** moved to "In Alarm" after the CPU load remained high for the required evaluation period.

### Phase 3: Automated Scaling & Recovery
* **Action:** The ASG launched new instances to distribute load and terminated them when the stress test ended.
* **Evidence (Scale-Out):** `Screenshot 2026-01-14 010040.png` shows the fleet expanded to 8 instances.
* **Evidence (Scale-In):** `Screenshot 2026-01-14 010201.png` shows the **Activity History** logs of successful instance terminations.

---

## 📊 Summary of Evidence

| Phase | Technical Observation | Proof |
| :--- | :--- | :--- |
| **Trigger** | CPU reached 100% capacity via `stress-ng`. | `Screenshot 2026-01-14 003551.png` |
| **Detection** | CloudWatch Alarm transitioned to "In Alarm" state. | `image_8bc8aa.png` |
| **Scale-Out** | ASG successfully provisioned new nodes to handle load. | `Screenshot 2026-01-14 010040.png` |
| **Scale-In** | ASG terminated nodes post-test to optimize cost. | `Screenshot 2026-01-14 010201.png` |

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
