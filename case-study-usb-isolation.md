# 🛠️ Case Study: USB Bus Bandwidth Congestion & Hardware Power Stabilization

## 📌 Executive Summary
High-polling rate peripherals and multi-device workstation setups often hit severe bandwidth limits and voltage fluctuations on integrated motherboard USB host controllers. This case study details the systematic root-cause diagnosis, power path isolation, and hardware resolution for persistent peripheral disconnects and input latency under heavy load.

---

## 🔍 Problem Statement & Initial Symptoms
* **Environment:** Custom Windows 11 workstation running multiple high-bandwidth, high-polling rate hardware peripherals.
* **Symptoms:** 
  * Intermittent device disconnects and data packet dropouts during peak system utilization.
  * Inconsistent power delivery across shared USB bus lines.
  * Latency spikes and peripheral reset loops in Windows.

---

## 🧪 Diagnostic & Root-Cause Isolation Workflow

### 1. Software & OS Diagnostics
* **Device Manager Audit:** Inspected device status codes (`devmgmt.msc`). Found no direct driver corruption flags, but observed controller-level packet retry behavior.
* **Windows Event Viewer Inspection:** Queried `eventvwr.msc` under `Windows Logs -> System`. Identified repeated event IDs indicating USB root hub power state transitions and bus reset requests (`USBHUB3`).
* **Power Management Isolation:** Navigated to Windows Power Options and disabled `USB Selective Suspend` to eliminate OS-level power throttling as a primary cause.

### 2. Hardware & Signal Isolation
* **Bus Bandwidth Analysis:** Mapped out connected devices across internal motherboard headers. Confirmed that multiple high-data peripherals were sharing a single USB root hub controller, exceeding total bus bandwidth capacity.
* **Voltage Drop Diagnostics:** Isolated potential voltage drop behavior when multiple non-powered peripherals drew bus power simultaneously from the main system board.

---

## 🛠️ Hardware Resolution & Architecture Change

To permanently eliminate controller congestion and supply steady voltage, a physical expansion architecture change was executed:

1. **Dedicated Host Controller Installation:** Installed a **StarTech 4-Port PCIe USB Expansion Card** into a dedicated PCI Express slot to offload high-polling peripherals from the motherboard’s native USB host controller.
2. **Dedicated PSU Power Delivery:** Connected direct **SATA power from the Power Supply Unit (PSU)** straight into the PCIe card board header, ensuring isolated, constant 5V power rails independent of motherboard power trace limits.
3. **Bandwidth Rebalancing:** Segregated high-throughput devices onto dedicated channels to balance overall polling rates across separate physical host controllers.

---

## ✅ Verification & Results

* **Stress Testing:** Conducted extended stress-testing across all connected peripherals operating at maximum polling rates simultaneously.
* **Event Log Audit:** Verified zero hardware disconnect events, zero `USBHUB3` warnings, and zero controller resets in Windows Event Viewer over extended operational periods.
* **Outcome:** Reached 100% data stability, zero disconnects, and steady power delivery across all active peripherals.

---

**Technologies & Tools Used:** Windows Event Viewer (`eventvwr.msc`), Device Manager, Command Line (CMD), PCIe Hardware Architecture, SATA Power Integration, USB Controller Bandwidth Management.
