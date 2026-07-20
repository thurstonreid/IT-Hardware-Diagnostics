# IT Hardware Diagnostics & System Isolation Notes

## System Isolation Log: USB Bus Saturation & Power Stabilization

### Environment & Issue Summary
* **Environment:** Custom Windows 11 workstation running high-polling rate peripheral hardware.
* **Symptom:** Device disconnects, latency spikes, and USB controller resets during high system load.

---

### Diagnostic & Isolation Workflow

1. **Event Log & Driver Audit:**
   * Checked Device Manager (`devmgmt.msc`) for driver status. No yellow flags raised, but controller packet retries were present.
   * Queried Windows Event Viewer (`eventvwr.msc`) under `Windows Logs -> System`. Identified recurring `USBHUB3` event warnings indicating root hub power state reset requests.

2. **Power & Bandwidth Isolation:**
   * Checked Windows Power Options and disabled `USB Selective Suspend` to verify the OS was not throttling port power.
   * Analyzed host controller assignments. Confirmed multiple high-polling peripherals were sharing a single internal motherboard USB root hub, exceeding total bus bandwidth limits under load.

---

### Hardware Resolution & Configuration

1. **Dedicated Host Controller:**
   * Installed a StarTech 4-Port PCIe USB Expansion Card into an open PCI Express slot to offload peripherals from the motherboard's built-in host controller.

2. **Power Path Isolation:**
   * Connected dedicated SATA power directly from the Power Supply Unit (PSU) to the expansion card. This isolated 5V bus power delivery from the motherboard traces.

3. **Bus Balancing:**
   * Reallocated high-bandwidth devices onto separate physical host channels to balance total polling rates across dedicated controllers.

---

### Verification & Results

* **Stress Testing:** Ran continuous high-bandwidth load testing across all connected peripherals simultaneously.
* **Log Verification:** Audited Windows Event Viewer over extended uptime. Confirmed zero disconnect events, zero `USBHUB3` warnings, and consistent 5V rail stability.

---

**Tools & Technologies:** Windows Event Viewer (`eventvwr.msc`), Device Manager, PCIe Expansion Architecture, SATA Power Integration, USB Host Controller Management.
