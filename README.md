# Steering_Wheel_Applications
This project is a single-joint compliance control demo for a robotic joint based on “zero-difference cloud control,” offering an initial simulation of steering wheel force feedback. It is still in its early stages, and you are welcome to fork it and further refine or improve it.


# Hardware Components

<table>
  <tr>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/23509ce5bc056e5d.png" width="150"><br>
      ***eRob110H100I-BHM-18ET***<br>
      (100:1 ratio, high-precision encoder, EtherCAT version)<br>
      V6 version - 1 unit
    </td>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/79058aa55b24f3f5.png" width="150"/><br>
      **Connecting Flange**<br>
      1 piece
    </td>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/75da95701bb55b7f.png" width="150"/><br>
      **Steering Wheel**<br>
      1 unit
    </td>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/351a309457a541c5.png" width="150"><br>
      **ePower Power Supply**<br>
      1 unit
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/0afd86a3dc2ecafd.png"  width="150"><br>
      **eRob Custom Cable**<br>
      1 piece
    </td>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/75ccbb54d55243be.png"  width="150"><br>
      **Network Cable**<br>
      1 piece
    </td>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/90970a17c3a9423a.png" width="150"><br>
      **Inner Hexagon Cylinder Head Screws**<br>
      M4 × 30 - 4 pieces
    </td>
    <td align="center">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/54f3a5929d34a501.png" width="150"><br>
      **Inner Hexagon Countersunk Head Screws**<br>
      M5 × 20 - 10 pieces
    </td>
  </tr>
</table>

# Development Environment

## 1. Experimental Setup

<table>
  <tr>
    <td width="40%">
      <img src="https://cdn-fusion.imgcdn.store/i/2025/686c26176d922d50.png" alt="Experimental Setup" style="width:100%; border:1px solid #ddd; border-radius:4px; padding:5px;">
    </td>
    <td>
      <p>
        <b>Assembly:</b><br>
        - Fix the joint module.<br>
        - Install the connecting flange.<br>
        - Tighten the screws and fix the flange.<br>
        - Secure the steering wheel to the flange using the four screws provided.<br>
      </p>
      <p>
        <b>Power Supply:</b><br>
        - Use the ePower power supply to generate 48V DC to power the joint module.<br>
        - Connect the system to the network for communication between the joint module and the host controller.<br>
      </p>
      <p>
        <b>Reference:</b><br>
        - Refer to Chapter 6, <i>"TwinCAT Master Control"</i>, in the manual 
        <b><a href="https://www.zeroerr.com/support/download">eRob CANopen and EtherCAT User Guide v1.9.pdf</a></b>,
        for detailed configuration instructions on setting up PDO mappings, controlling operations, and configuring ZeroErr EtherCAT slave stations under Beckhoff TwinCAT3.<br>
      </p>
    </td>
  </tr>
</table>


## 2. PDO Configuration

Follow the user manual to add PDO parameters and map these variables to corresponding predefined variables in the program.

| **Name**                        | **Address** | **Align**                |
|---------------------------------|-------------|--------------------------|
| Transmit PDO                    | 0x1A00      |                          |
| Dual encoder difference value   | 0x2241      | GVL.DualPosDiff          |
| Position actual value           | 0x6064      | GVL.actPosition          |
| Actual velocity                 | 0x606C      | GVL.actVelo              |
| Receive PDO                     | 0x1600      |                          |
| Profile acceleration            | 0x6083      | GVL.udiProAcc            |
| Profile deceleration            | 0x6084      | GVL.udiProDec            |
| Modes of operation              | 0x6060      | GVL.siOperationMode      |
| Target velocity                 | 0x60FF      | GVL.targetVelo           |


## 3. Program Usage

### 3.1 Import Program
- Import the following files into your project:
  - `Main.TcPOU`
  - `KalmanFilter.TcPOU`
- Create a new `GVL.TcGVL` under **GVLs**.

---

### 3.2 Import Configuration File
- Modify the parameters in `Offset` under `Main.TcPOU` according to the configuration parameters output by the data calibration program.

---

### 3.3 Run the Program
1. **Compile and Activate Configuration**:
   - Compile the program → Restart TwinCAT (Config Mode) → Activate Configuration.

2. **Enable the Joint**:
   - In the **Receive PDO**, manually force the **Control word** from `6` (disabled) to `47` (enabled) to enable the joint.

3. **Start the Program**:
   - Load the program and change the parameter `iStep` from `0` to `1` to start the program.

4. **(Optional) Monitor Joint Status**:
   - Use the oscilloscope module to observe the joint's operating status.

5. **Adjust Parameters**:
   - Adjust parameters such as `K`, `B`, and `M` to simulate different scenarios of compliance control.

6. **Stop the Program**:
   - Change the parameter `iStep` from `15` to `1000` to stop the program.

7. **Disable the Joint**:
   - Wait for `iStep` to automatically reset to `0`, and for the joint speed to reach `0`. Then, manually force the **Control word** from `47` (enabled) to `6` (disabled) to disable the joint.
