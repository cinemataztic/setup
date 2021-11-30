This guide describes how to automate CineGame in a Doremi setup.
It consist of the following 3 steps: 
1. Doremi setup 
2. Macro setup
3. Testing

## 1. Device setup
1. Open Device Manager
2. Add new device of type "Raw"
3. Add device information
4. Input the (fixed) IP address the device will receive cues on.
5. Port (defaults to 3120), unless otherwise specified in the cloud admin interface
6. Device is now ready to receive cues

_It is a good idea to have set up the machine to have a reserved IP address on the networks DHCP server, to ensure it has the same address between reboots._
![doremi-device-setup](https://user-images.githubusercontent.com/6972570/144060542-738197ec-8e29-4b8a-8124-2300948be89a.jpg)

## 2. Macro setup
The content playlists on the CineGame machines can have several "targets" e.g. "adults" or "family", which can be used to trigger different games in front of different shows. Triggering start of these games can be done by including a cue to the CineGame machine in the macro.

1. Open Macro Editor: (Menu→Doremi Labs, Inc. → Macro Editor)
2. Click the Add button (+)
3. Give the cue a name (e.g. Start_CineGame)
4. Description (e.g. stat CineGame on CinemaTaztic DCH-P)
5. Click OK

![start_cinegame](https://user-images.githubusercontent.com/6972570/144060764-1b282d24-8890-44b0-b7b1-e146fda940e0.jpg)

6. Select the newly created Automation Cue and click Insert a new Action

![macro_editor_cinegame](https://user-images.githubusercontent.com/6972570/144061223-1508ee41-cc68-4516-a142-25f4ffcb79fd.jpg)

7. Choose **Send Message**

<img width="609" alt="macro editor options" src="https://user-images.githubusercontent.com/6972570/144061410-5de0b74a-9e63-4be3-ab0a-f1ae5c624ddc.png">

8. Set **Message label** to e.g. "gameStart"
9. Choose **CineGame** in the **Device name** dropdown
10. Set **Message type** to **Text**
11. Use Doremi's virtual keyboard to create the curly braces { } used in the message and be sure the "" are formatted correctly (not as " ").

![send-message](https://user-images.githubusercontent.com/6972570/144061815-6c149919-73fc-4c3b-a3ba-0b3d67972439.jpg)
![doremi_keyboard](https://user-images.githubusercontent.com/6972570/144061851-2b04b188-cec5-499c-8d5c-efae9ecd766d.png)

