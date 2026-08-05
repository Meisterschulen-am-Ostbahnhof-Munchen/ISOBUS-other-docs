# ISO/OSI Layer Model

## General Information:

The ISO/OSI reference model (also called the OSI model) is a theoretical model that describes communication between different computers and networks. It was developed by the International Organization for Standardization (ISO) and is specified in the standard ISO 7498-1.


The OSI model consists of seven layers, each with specific tasks in the communication process:

Physical layer: Transmission of bits over the physical medium (cable, radio, fiber optic, etc.)

Data link layer: Error detection and correction, device addressing (MAC address)

Network layer: Routing of packets between different networks

Transport layer: Division of data into packets, ensuring reliability and complete transmission

Session layer: Establishment, maintenance, and termination of sessions between applications

Presentation layer: Conversion of data into a format understandable by the application

Application layer: Interface between applications and the network, e.g., email, web browser, or file transfer

The OSI model serves as a reference for the development of network protocols and architectures. It enables the integration and standardization of various network technologies, allowing devices from different manufacturers to communicate with each other.

## ISO/OSI Reference Model and SAE J1939

SAE J1939 is a protocol specifically designed for use in commercial vehicles and machinery. It is based on the CAN bus standard and uses the ISO/OSI reference model as the foundation for its layered architecture.

The SAE J1939 protocol architecture consists of five layers:

Application Layer: This layer defines the specific application data exchanged between devices on the network.

Transport or Service Layer: This layer is responsible for dividing the data into smaller packets and ensuring the reliability of data transmission.

Network Layer: This layer manages addressing and the exchange of packets between different networks.

Data Link Layer: This layer provides error detection and correction, as well as access to the medium (CAN bus).

Physical Layer: This layer handles the electrical and physical transmission of data over the medium (CAN bus).


Like the OSI model, the layered architecture of SAE J1939 enables the interoperability and integration of various devices from different manufacturers into a network. This facilitates the development and implementation of applications for commercial vehicles and machinery based on SAE J1939.

[https://www.csselectronics.com/pages/j1939-explained-simple-intro-tutorial](https://www.csselectronics.com/pages/j1939-explained-simple-intro-tutorial)

![](https://cdn.shopify.com/s/files/1/0579/8032/1980/files/j1939-osi-model-7-layer-standards-sae.svg)

## ISO/OSI Reference Model and ISO 11783

[https://www.csselectronics.com/pages/isobus-introduction-tutorial-iso-11783](https://www.csselectronics.com/pages/isobus-introduction-tutorial-iso-11783)

![](https://cdn.shopify.com/s/files/1/0579/8032/1980/files/isobus-osi-model-layer-iso-11783.svg)