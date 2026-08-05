# PGN

### **Chapter: What is a PGN (Parameter Group Number)?**

#### **Introduction**
In the world of mobile machinery, especially in agricultural and construction engineering, communication between different electronic control units (ECUs) plays a central role. To make this communication efficient and standardized, the **ISOBUS protocol** was developed, which is based on the **CAN bus (Controller Area Network)**. A key element of this protocol is the **Parameter Group Number (PGN)**, which enables the unique identification of messages. This chapter explains what a PGN is, how it is structured, and what role it plays in ISOBUS communication.

---

#### **Definition of a PGN**
A **Parameter Group Number (PGN)** is a 24-bit number that identifies a specific message or data set within the ISOBUS network. Each PGN represents a logical grouping of data that is transmitted together. This data can contain information such as the speed of a machine, the status of an attachment, or control commands for a hydraulic system.

---

#### **Structure of a PGN**

A PGN consists of three main components:

1. **Extended Data Page (EDP)**: 1 bit

- Indicates whether the PGN is located on the Extended Data Page.

- Value: 0 (default) or 1 (extended).

2. **Data Page (DP)**: 1 bit

- Helps distinguish between different message types.

- Value: 0 (default) or 1 (alternative).

3. **PF (Parameter Group Format) and PS (Parameter Group Specific)**:

- **PF (8 bits)**: Defines the format of the PGN.

- If PF < 240, then the PGN is a **peer-to-peer message** (targeted).

- If PF ≥ 240, then the PGN is a **broadcast message** (directed to all devices).

- **PS (8 bits)**:

- If PF < 240: PS specifies the destination address.

- If PF ≥ 240: PS is used to further specify the PGN.

The complete PGN is formed by combining EDP, DP, PF, and PS and is represented as a 24-bit value.


---

#### **Example of a PGN**

Let's take PGN **65096 (Wheel-based Speed and Distance – WBSD)** as an example:

- **EDP**: 0
- **DP**: 0
- **PF**: 254
- **PS**: 72
- **PGN**: 0x00FE48 (hexadecimal) or 65096 (decimal).

This PGN identifies a message containing information about a machine's wheel-based speed and distance.

--

#### **Role of PGNs in ISOBUS Communication**

1. **Message Identification**:

Each PGN uniquely identifies a specific message or data record. This allows ECUs to know what type of data they are receiving or sending.

2. **Standardization**:

Using PGNs standardizes communication between devices from different manufacturers. This enables interoperability between tractors, implements, and other machinery.

3. **Efficient Data Transmission**:

PGNs group related data into a single message, increasing data transmission efficiency and reducing network traffic.

4. **Flexibility**:

PGNs can represent both targeted (peer-to-peer) and general (broadcast) messages, increasing communication flexibility.

---

#### **Types of PGNs**

1. **Broadcast PGNs**:

- Sent to all devices on the network.

- Examples: speed, RPM, operating hours.

2. **Peer-to-Peer PGNs**:

- Sent to a specific device.

- Examples: control commands for a specific implement.

3. **Proprietary PGNs**:

- Defined by manufacturers for specific applications.

- Are not part of the ISOBUS standard.

---

#### **Summary**

A **Parameter Group Number (PGN)** is a central element of ISOBUS communication. It uniquely identifies a message or data record, thus enabling the standardized and efficient transfer of data between different electronic control units. The use of PGNs ensures interoperability between devices from different manufacturers, which is particularly important in agricultural and construction engineering.

---

# PGN List



```