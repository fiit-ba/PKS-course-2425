# **Assignment: Communication application using the UDP protocol** ([SK](README.md))

## **Assignment Objective**

Design and implement a P2P(PEER-TO-PEER) application with a custom protocol built on top of UDP (User Datagram Protocol) at the transport layer of the TCP/IP network model. The application will allow communication between two participants in a local Ethernet network, including text exchange and the transfer of arbitrary files between computers (nodes). Both nodes will function simultaneously as both receivers and senders.

The assignment consists of two parts: theoretical and practical. In the theoretical part, you will design your protocol over UDP. In the practical part, you will implement and demonstrate its functionality in communication between two nodes. You will also highlight the adjustments made during implementation compared to the original proposal.

## **Theoretical part - Protocol Design**

The requirements for the protocol design are as follows:

- establishing a connection between two parties and negotiating the connection parameters (such as TCP handshake),
- allow data to be sent in fragments of a size specified from user input,
- verify the integrity of the message sent on the recipient side,
- resend lost or corrupted message,
- mutual checking between nodes if the participant on the other side of the link is still active,
- the possibility to deliberately create an error in one of the transmitted messages in order to simulate corrupted data,
- other essentials that you consider important.

The output of this section is documentation in which you will present the design of your protocol and the implementation plan for individual mechanisms. You will then discuss this documentation with your instructor during a checkpoint, where you will receive feedback to incorporate into the practical part. The documentation must contain the following sections:

- the structure of the headers of your protocol,
- a description of the method for checking the integrity of the transmitted message (e.g., if you choose CRC16, you will describe how it is calculated; apply the same approach for other methods),
- a description of the method for ensuring reliable data transmission (ARQ),
- a description of the method to maintain the open and persistent connection (Keep-Alive),
- diagrams describing the expected behavior of your node/nodes, using UML diagrams such as sequence, activity, and state diagrams (use appropriate tools such as [miro](https://miro.com) or [draw.io](https://draw.io/), not Paint).

The evaluation will consider the clarity and comprehensibility of the submitted documentation and the quality of the proposed solution. The documentation must also include formal parts such as a cover page, table of contents, page numbering, etc. The practical section provides more specific information about individual program requirements.

## **Practical part - implementation of P2P communicator + additional implementation**

In this section, implement the P2P communicator according to your design, taking into account the feedback from the checkpoint. Your evaluation will be based on the fulfillment of the following tasks:

#### 1. Establish connection and verify it

Establish a P2P connection between two nodes, agree the connection parameters, and verify the connection by sending text in both directions of communication. The user interface on each node must allow the following parameters to be set:

- the port on which the node listens,
- the IP address and port of the target node to which the text/file will be sent.

Demonstrate a successful transmission using Wireshark (WS), so that you can identify the messages belonging to your protocol in the correct order and show the individual fields of your protocol.

#### 2. Transfer text/file - fragmentation

In computer networks, fragmentation refers to dividing large data blocks into smaller fragments before sending them over the network. This process is often necessary to ensure optimal data transmission, either due to network congestion or the characteristics of the given transmission medium.

Your task is to implement the fragmentation process with the following features: The node's user interface must allow dynamic fragment size adjustment during the program's runtime (between transmissions). When transferring text/files, two scenarios are possible (must be implemented and successfully demonstrated to get points):

- _transfer without fragmentation_ - the size of the text/file is less than or equal to the set maximum fragment size, in which case the text/file is transmitted as a single unit without unnecessary padding,
- _transfer with fragmentation_ - the size of the text/file is larger than the set maximum fragment size, and the sending node divides the file into smaller fragments, which are sent individually.

The fragment size setting must be limited so that the user cannot choose a size that would cause re-fragmentation at the data link layer. Control messages, which manage communication, are always transmitted without fragmentation. The user must be able to select a file from any folder in the device's file system and send it.

In the case of a transfer with fragmentation, the following information must be displayed on the respective nodes:

- _sending node_ - displays the name of the file being sent (this is ignored for text messages), the size of the text/file, the number of fragments sent, and the fragment size. If the last fragment is a different size than the other fragments, the size of the fragment must also be displayed.
- _receiving node_ - displays a message for each received fragment with its sequence number and information on whether the fragment was transferred without errors.After receiving the entire text/file, the node displays a message about successful reception, the transfer duration, the text/file size, and the absolute path where the file was saved.

#### 3. Transfer text/file - data corruption and loss

In network communication, data may sometimes be lost or corrupted during transmission. For this reason, your P2P communicator must implement an error-checking mechanism and ensure the retransmission of lost or corrupted data fragments. The communicator must support positive (ACK) and negative (NACK) message acknowledgments.

You implement the method of reliable data transmission, Automatic Repeat Request (ARQ). The individual methods are scored based on their complexity. You can choose from the following ARQ methods:

- Stop & Wait (S&W)
- Go Back-N (GBN)
- Selective Repeat (SR)
- Enhanced Go Back-N/Selective Repeat (dynamic sliding window, optimization of the number of ACK messages, etc.)

Successfully transferring text/files, even when simulating transmission errors, is essential to fulfilling this task. This includes properly implementing error detection and the ARQ method. The section [Literature](#Literature) provides more detailed information about the individual methods.

#### 4. Keep the connection alive (Keep-Alive)

This technique is used in network protocols to keep an active connection between two nodes during their inactivity. Its goal is to minimize the number of new connections established, thereby reducing the latency associated with creating each connection. It also allows for monitoring the connection status and verifying whether both sides are active, improving connection reliability and enabling faster responses during a participant's disconnection.

Implement your Keep-Alive mechanism within the communicator with the following features:

- use custom signaling messages,
- start monitoring the connection immediately after it is established,
- send heartbeat messages every 5 seconds if there is no activity between the devices,
- if one side of the connection does not receive a response to 3 consecutively sent heartbeat messages, it considers the connection terminated,
- the node that determines the connection has been terminated/interrupted displays information about the termination in the user interface,
- the communicator should be able to react if the connection suddenly fails during text/file transmission (e.g., disconnecting the network cable). In this case, it checks the status of the other side by sending heartbeat messages and, based on the result, performs one of the following actions:
  - if no response is received to any of the sent heartbeat messages, it declares the connection terminated and indicates that the text/file transfer has failed,
  - if the connection is restored, i.e., a response is received to at least the last heartbeat message, it attempts to resume the text/file transfer, including resending fragments that were not successfully delivered during the connection failure.

#### 5. Lua Script in Wireshark

Create a Lua script to analyze your protocol over UDP and display its content in Wireshark. The script will recognize your custom protocol based on the port, decode the individual fields of the protocol, and display their values directly in the Wireshark analysis output. Use color coding to distinguish messages that carry useful data from control messages. An example can be found at this [link](https://wiki.wireshark.org/Lua/Examples).

#### 6. Final Documentation

The student will complete the protocol design documentation with the following chapters:

- changes made during the implementation compared to the proposed solution,
- include diagrams if they were missing at the checkpoint,
- provide at least one comprehensive example of a test scenario with screenshots from Wireshark (opening a connection, sending without transmission error, sending with transmission error and subsequent correction, closing the connection),
- conclusion and used sources.

The clarity and comprehensibility of the submitted documentation and the quality of the overall solution will be assessed.

#### 7. Implementation quality

The overall quality of the communicator's implementation will be assessed, which may include the following aspects:

- user-friendliness of the command-line interface (CLI) (ease of use, clarity of outputs, etc.); it is possible to create a clear and user-friendly program interface even in the CLI,
- transmission speed,
- stability of the communicator (frequency of crashes during the presentation),
- the ability of the communicator to adapt to various user requirements without a restart.

## Acceptance protocol

All conditions must be met at the final submission of the assignment for it to be accepted and scored. Otherwise, you will get 0 points. The nodes are equal for P2P, so both must meet the conditions.

1. The program must be implemented in one of the following programming languages: C, C++, C#. Rust can be used only if approved by the instructor.
2. The user must be able to set the listening port on the node.
3. The user must be able to set the target IP address and port.
4. The user must be able to select the maximum fragment size on a node and dynamically change it while the program is running before sending the text/file (this does not apply to control messages).
5. The node must be able to display the following information about the received/sent text/file:

   - the name and absolute path of the file on the node,
   - the fragment size and number of fragments, including the total length (the last fragment may have a different size than the previous fragments).

6. Simulate a transmission error by sending at least one erroneous fragment during the transfer of text and files (an error is deliberately introduced into the data part of the fragment or the checksum, meaning the receiving side detects the error during transmission).
7. The receiving side must be able to notify the sender of the correct and incorrect delivery of fragments. In the case of incorrect delivery of a fragment, it must request the resending of the corrupted data.
8. The ability to send a 2MB file within 60 seconds and save it on the receiving node as the same file.
9. The ability to set the location on the node where the file will be saved upon receipt.

Using the following table, you can check whether you meet all the acceptance conditions before submitting the assignment. You will only submit the assignment if you gain 'YES' 9/9.

<table style="text-align: center;">
    <thead>
    <tr>
        <th>Condition</th>
        <th>Status (YES/NO)</th>
    </tr>
    </thead>
    <tbody>
        <tr>
            <td style="text-align: center;">1</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">2</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">3</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">4</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">5</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">6</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">7</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">8</td>
            <td></td>
        </tr>
        <tr>
            <td style="text-align: center;">9</td>
            <td></td>
        </tr>
    </tbody>
</table>

## Submission Phases and Schedule

### Checkpoint (CP)

One .ZIP file named <ais_login>.zip, e.g., xpacket.zip, to be submitted, containing the following files:

- Documentation with the design of your own protocol in PDF format.
- Application with the implementation of the task '[Establish connection and verify it](#1-establish-connection-and-verify-it)'

#### Submission deadline to AIS: 21.10.2024 23:59

### Final Submission (FS)

One .ZIP file named <ais_login>.zip, e.g., xpacket.zip, to be submitted, containing the following files:

- Final documentation
- Final application without hidden folders (starting with the character "." e.g., .vscode, .config, .idea ...)

#### Submission deadline to AIS: 25.11.2024 23:59

### Additional implementation(AI)

The student will implement the assigned task into the submitted communicator during the exercise and demonstrate its functionality without failures or error messages.

#### Scheduled Dates: from 26.11.2024 to 28.11.2024, during laboratory practice

## Score table

<table>
<thead>
  <tr>
    <th>Phases</th>
    <th>Task<br>number</th>
    <th>Task</th>
    <th style="text-align: center;">Max<br>score</th>
    <th style="text-align: center;">Min<br>score</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>CP</td>
    <td></td>
    <td><b>Protocol Design</b>
    <td style="text-align: center;">3</td>
    <td style="text-align: center;" rowspan="2">3</td>
  </td>
  </tr>
  <tr>
    <td>CP</td>
    <td>1</td>
    <td><b>Establish connection and verify it</b>
    <td style="text-align: center;">3</td>
  </td>
  </tr>
  <tr>
    <td>FS</td>
    <td>2</td>
    <td><b>Transfer text/file - fragmentation</b>
    <td style="text-align: center;">4</td>
    <td style="text-align: center;" rowspan="6">9</td>
  </td>
  </tr>
  <tr>
    <td>FS</td>
    <td>3</td>
    <td><b>Transfer text/file - data corruption and loss</b>
    <td>6 - Enhanced GBN/SR<br>
    5 - SR<br>
    3 - GBN<br>
    1 - S&W</td>
  </td>
  </tr>
  <tr>
    <td>FS</td>
    <td>4</td>
    <td><b>Keep the connection alive (Keep-Alive)</b>
    <td style="text-align: center;">3</td>
  </td>
  </tr>
  <tr>
    <td>FS</td>
    <td>5</td>
    <td><b>Lua Script in Wireshark</b>
    <td style="text-align: center;">2</td> 
  </td>
  </tr>
  <tr>
    <td>FS</td>
    <td>6</td>
    <td><b>Final Documentation</b>
    <td style="text-align: center;">2</td> 
  </td>
  </tr>
  <tr>
    <td>FS</td>
    <td>7</td>
    <td><b>Implementation quality</b>
    <td style="text-align: center;">1</td> 
  </td>
  </tr>
  <tr>
    <td>AI</td>
    <td></td>
    <td><b>Additional implementation</b>
    <td style="text-align: center;">1</td>
    <td style="text-align: center;">0,5</td>
  </td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td><b>Total</b>
    <td style="text-align: center;">25</td>
    <td></td>
  </td>
  </tr>
</tbody>
</table>

## Literature

1. [Stop & Wait, Sliding Window Protocol](https://www.youtube.com/watch?v=LnbvhoxHn8M&ab_channel=NesoAcademy)
2. [Selective repeat](https://www.youtube.com/watch?v=WfIhQ3o2xow&t=5s&ab_channel=NesoAcademy)
3. [Go Back-N](https://www.youtube.com/watch?v=QD3oCelHJ20&ab_channel=NesoAcademy)
