# Qu Xinyu Hiro's Document
## Week Final report
tomorrow is our showcase！！！
Special Thanks：——By Xinyu
Throughout this integration process, I have gained valuable insights and expanded my understanding of hardware development. Despite considering myself proficient in hardware, I realized that my knowledge was relatively shallow. The key takeaway was the recognition that consulting relevant samples when working with new components is the most efficient learning method.
Moreover, this project introduced me to using VS Code for hardware development, a tool I had not utilized previously. Learning to install various libraries significantly simplified the entire process. Each discussion with Jeff highlighted the importance of paying attention to details and adopting a meticulous approach to hardware testing. This included the realization that verifying each component individually before assembly is crucial for ensuring accuracy and functionality.
In essence, the integration experience has been a rich learning journey, demonstrating the importance of continuous exploration, leveraging available resources, and collaborating with experienced individuals to deepen one's understanding of hardware development.

## Week Fifteen report
In the process of utilizing Photon for hardware development, I explored two main approaches. Initially, I employed a doubler to replicate Photon's pins and integrated a datalogging featherwing. The notable feature of this board is the built-in TF card reader, allowing data processing based on timestamps. However, this method necessitates manually removing the SD card and plugging it into a computer for data retrieval, which posed some challenges in the code implementation.

To address these issues, with assistance from Jeff, I transitioned to a second approach. This involved utilizing Photon directly for computation and transmitting data to a virtual server established on a computer. The system then generates WAV files directly in the target computer's designated folder. The fundamental aspects of the system remained consistent, including the use of a button to initiate recording, a subsequent press to cease recording, and the incorporation of a maximum recording duration to enable timed recording termination.

### Technical Details:
First Approach: Doubler and Datalogging Featherwing

Implementation of a doubler to duplicate Photon pins.
Integration of a datalogging featherwing with a TF card reader.
Data processing based on timestamps.
Manual extraction of the SD card for data retrieval.
Code challenges were encountered and needed resolution.
Second Approach: Direct Photon Computation and Data Transmission

Elimination of doubler and direct use of Photon for computation.
Data transmission to a virtual server established on a computer.
Real-time generation of WAV files in the designated folder on the target computer.
Retention of the button control mechanism for recording initiation and cessation.
Incorporation of a maximum recording duration for timed recording termination.
Conclusion:
The transition to the second approach not only streamlined the hardware setup but also resolved the challenges associated with the first method. By leveraging Photon's computational capabilities and transmitting data directly to a virtual server, the system now offers a more efficient and user-friendly solution for hardware development and data logging. The use of button controls and timed recording termination further enhances the system's usability and flexibility in various applications.

### Code: (the final version)
//  * Project myProject
//  * Author: Your Name
//  * Date:
//  * For comprehensive documentation and examples, please visit:
//  * https://docs.particle.io/firmware/best-practices/firmware-template/


#include "Microphone_PDM.h"
#include "Particle.h"


SYSTEM_THREAD(ENABLED);
SYSTEM_MODE(SEMI_AUTOMATIC);


#define buttonPin D3


SerialLogHandler logHandler;


// If you don't hit the setup button to stop recording, this is how long to go before turning it
// off automatically. The limit really is only the disk space available to receive the file.
const unsigned long MAX_RECORDING_LENGTH_MS = 30000;


// This is the IP Address and port that the server.js node server is running on.
//IPAddress serverAddr = IPAddress(10,44,92,177); //10.44.92.177 Hiro's IP address
IPAddress serverAddr = IPAddress(10,44,225,153); //nancy:10.44.225.153


int serverPort = 7123;


TCPClient client;
unsigned long recordingStart;


enum State { STATE_WAITING, STATE_CONNECT, STATE_RUNNING, STATE_FINISH };
State state = STATE_WAITING;


// Forward declarations
void buttonHandler(void);




void setup() {
   interrupts();


   Particle.connect();


   // Register handler to handle clicking on the SETUP button
   //System.on(button_click, buttonHandler);
   attachInterrupt(buttonPin, buttonHandler, RISING);


   //Serial.begin(115200);


   // Blue D7 LED indicates recording is on
   pinMode(D7, OUTPUT);
   digitalWrite(D7, LOW);


   // Optional, just for testing so I can see the logs below
   // waitFor(Serial.isConnected, 10000);


   // output size is typically Microphone_PDM::OutputSize::UNSIGNED_8 or icrophone_PDM::OutputSize::SIGNED_16
   // and must match what the server is expecting (set on the command line)


   // Range must match the microphone. The Adafruit PDM microphone is 12-bit, so Microphone_PDM::Range::RANGE_2048.


   // The sample rate default to 16000. The other valid value is 8000.


   int err = Microphone_PDM::instance()
       .withOutputSize(Microphone_PDM::OutputSize::SIGNED_16)
       .withRange(Microphone_PDM::Range::RANGE_2048)
       .withSampleRate(16000)
       .init();


   if (err) {
       Log.error("PDM decoder init err=%d", err);
   }


   err = Microphone_PDM::instance().start();
   if (err) {
       Log.error("PDM decoder start err=%d", err);
   }


}


void loop() {


   switch(state) {
   case STATE_WAITING:
       // Waiting for the user to press the SETUP button. The setup button handler
       // will bump the state into STATE_CONNECT
       break;


   case STATE_CONNECT:
       // Ready to connect to the server via TCP
       if (client.connect(serverAddr, serverPort)) {
           // Connected
           Log.info("starting");


           recordingStart = millis();
           digitalWrite(D7, HIGH);


           state = STATE_RUNNING;
       }
       else {
           Log.info("failed to connect to server");
           state = STATE_WAITING;
       }
       break;


   case STATE_RUNNING:
       Microphone_PDM::instance().noCopySamples([](void *pSamples, size_t numSamples) {
           client.write((const uint8_t *)pSamples, Microphone_PDM::instance().getBufferSizeInBytes());
       });


       if (millis() - recordingStart >= MAX_RECORDING_LENGTH_MS) {
           state = STATE_FINISH;
       }
       break;


   case STATE_FINISH:
       digitalWrite(D7, LOW);
       client.stop();
       Log.info("stopping");
       state = STATE_WAITING;
       break;
   }
}




// button handler for the SETUP button, used to toggle recording on and off
void buttonHandler(void) {
   switch(state) {
   case STATE_WAITING:
       if (WiFi.ready()) {
           state = STATE_CONNECT;
       }
       break;


   case STATE_RUNNING:
       state = STATE_FINISH;
       break;
   }
}




## Week Fourteen report
This week, during Thanksgiving, I did not go out for leisure. Instead, I spent the time working on hardware projects.
## Week Thirteen report
this week is Thanks giving, Happy Thanksgiving
But I do not have any plan to spend my holiday,and I am still working on the hardware part of the final project.


## Week twlves repot
### Hardware Development for Final Project

This week, my focus was on the hardware aspect of our final project. With Jeff's assistance, I successfully learned how to utilize the doubler and data logging hardware. Throughout this process, there were specific considerations that I needed to keep in mind.

### Using the Doubler

1. **Power Requirements:** Ensure the doubler is connected to the correct power source to prevent any potential damage to the device.
2. **Pin Connections:** Pay careful attention to ensure all pins are correctly connected to avoid signal transmission errors.
3. **Solder underneath:** I need to solder the line underneath the board for power supply

### Utilizing Data Logging

1. **Storage Capacity:** Be mindful of the data logging storage capacity, ensuring sufficient space is available for the required data.
2. **Sampling Frequency:** Adjust the sampling frequency as needed to balance data accuracy and efficient use of storage space.
3. **Data Format:** Ensure the selection of an appropriate data format for ease of subsequent processing and analysis.

## Implementation of Recording Functionality

In my efforts, I successfully implemented the capability to start and stop recording using buttons, saving the recordings as .wav files to the SD card.

### Button-Controlled Recording

1. **Hardware Connections:** Confirm proper button connections to the respective pins to trigger recording accurately.
2. **Interrupt Handling:** Use interrupt handling to ensure proper capture and processing of button press and release actions.
3. **State Management:** Implement a state machine or similar mechanism to ensure that pressing the button during recording does not trigger a repeat recording.

### Saving Recording Files

1. **File Naming:** Adopt meaningful file naming conventions for efficient file management and identification.
2. **File Path:** Ensure files are saved to the correct path on the SD card to prevent data loss or storage errors.
3. **Format Compatibility:** Confirm that .wav files are recognizable and usable by subsequent processing tools.

This week's work provided me with a deeper understanding of hardware operations and considerations. Simultaneously, I made progress in the implementation of recording functionality.

## Week eleven report
### Introduction

This week, our focus revolved around the outline of Project 4, which involves identifying compelling use cases for the technologies we have explored throughout the semester. The objective is to showcase these technologies at The Jacobs Showcase, targeting technical decision-makers, user-centered designers, and the general public. The technologies at our disposal include microcontrollers, sensors, actuators, digital modeling, simulation, fabrication tools, and LLM-based AI. During the week, I contemplated two options: either enhancing my second project, based on the Photon technology, to make it more product-oriented, or starting a completely new project, the details of which are still under consideration.

The primary challenge this week was deciding whether to enhance my existing project or start a new one. Both options have their merits and require careful consideration to align with the objectives of Project 4.
### Next Steps

For the upcoming week, I plan to:
Continue refining the scenarios for potential technology applications and begin assembling the necessary components for these scenarios.
Further develop the outline of the demonstration, ensuring that it effectively showcases the technology and enables audience engagement. Evaluate the advantages and disadvantages of each option (enhancing the existing project or starting a new one) and make a well-informed decision.
Seek input from peers and mentors to gain additional perspectives on the project direction.
Start building the groundwork for the selected project, preparing for a productive and efficient implementation phase.

This week marked the initiation of Project 4, and I am excited about the potential it holds for exploring and demonstrating the technologies we have learned about. The decision on whether to enhance my existing project or embark on a new one will play a significant role in shaping the direction of the project, and I look forward to the challenges and opportunities that lie ahead.
## Week ten report
### Introduction

Hello, I'm Xinyu, and today, I'm excited to introduce my project, 'Generative Pre-trained Transformer Input Design and Knowledge Management,' and how I applied this technology to my Chatbot prototype - Minime.

First, let me show you my Chatbot prototype - Minime. As you can see, Minime is a Chatbot that can intelligently answer questions, but it's not just a simple chatbot. It uses Generative Pre-trained Transformer technology and has gone through a series of experiments and knowledge management strategies to support it.

### About the Experimentation Process

In this project, I explored a series of experiments, including how to design inputs for the Generative Pre-trained Transformer model. Here are some key points I'd like to discuss:

- First, I want to talk about how to design inputs for the Generative Pre-trained Transformer model.
- Next, I adopted a knowledge management strategy to achieve Retrieval Augmented Generation, which is crucial for my Chatbot Minime.
- Next, I investigated the impact of token count and context length in my experiments.
- I also explored the role of the 'temperature' parameter in my experiments. Temperature is a form of question-and-answer fault tolerance, adjusting it in conjunction with the chatbot's question-and-answer monitor can help fine-tune instructions, making the conversation more closely resemble the expected progression.

### Impact on Human Experience Design

This technology has a profound impact on human experience design. This allows us to provide users with more intelligent and personalized experiences, whether it's on social media, customer support, or other fields.

### Impact on Engineering and Construction

Moreover, this technology has a significant impact on the fields of engineering and construction. It has the potential to enable us to build intelligent systems more efficiently, increase productivity, while also reducing costs.

### Ethical Considerations

Finally, let's talk about the ethical considerations of presenting the prototype as a digital twin. When using this technology, we must handle privacy and security issues carefully and ensure the legal use of data.

### Conclusion

In summary, my project aims to explore the application of Generative Pre-trained Transformer technology and how to apply it to the Chatbot prototype Minime. I deeply understand the importance of this technology in human experience design and the engineering field, while also emphasizing the necessity of ethical considerations.

If you have any questions about my project or want to know more, please feel free to leave a comment or send me a message. Thank you again for your attention, and I hope my project sparks more interesting discussions and research.

## Week 10 report Minime
I'm excited to introduce my project, 'Generative Pre-trained Transformer Input Design and Knowledge Management,' and how I applied this technology to my Chatbot prototype - Minime.
First, let me show you my Chatbot prototype - Minime.
As you can see, Minime is a Chatbot that can intelligently answer questions, but it's not just a simple chatbot. It uses Generative Pre-trained Transformer technology and has gone through a series of experiments and knowledge management strategies to support it.

### About the Experimentation Process

In this project, I explored a series of experiments, including how to design inputs for the Generative Pre-trained Transformer model. Here are some key points I'd like to discuss.

1. First, I want to talk about how to design inputs for the Generative Pre-trained Transformer model.
2. Next, I adopted a knowledge management strategy to achieve Retrieval Augmented Generation, which is crucial for my Chatbot Minime.
3. Next, I investigated the impact of token count and context length in my experiments.
4. I also explored the role of the 'temperature' parameter in my experiments.

### Impact on Human Experience Design
This technology has a profound impact on human experience design.
This allows us to provide users with more intelligent and personalized experiences, whether it's on social media, customer support, or other fields.
· Impact on Engineering and Construction
Moreover, this technology has a significant impact on the fields of engineering and construction.
I believe this will enable us to build intelligent systems more efficiently, increase productivity, while also reducing costs.
· Ethical Considerations
Finally, let's talk about the ethical considerations of presenting the prototype as a digital twin.When using this technology, we must handle privacy and security issues carefully and ensure the legal use of data.

### Conclusion
In summary, my project aims to explore the application of Generative Pre-trained Transformer technology and how to apply it to the Chatbot prototype Minime.
I deeply understand the importance of this technology in human experience design and the engineering field, while also emphasizing the necessity of ethical considerations.
Thank you for watching. If you have any questions about my project or want to know more, please feel free to leave a comment or send me a message. Thank you again for your attention, and I hope my project sparks more interesting discussions and research.

## Week nine report

### Introduction
This weekly report summarizes my progress and activities during the past week as part of the "Mini Me" project, which focuses on designing an interaction interface using Large Language Models (LLMs) to simulate a personalized knowledge portfolio. In this report, I will provide an overview of the tasks I have completed and insights gained during the week.

### Project Overview
The "Mini Me" project aims to create an interactive portfolio that simulates a personalized interaction, exposing my knowledge as generated and captured throughout the DESINV 202 course. This project involves the design and management of knowledge sets, making informed decisions about what information to include and how to include it. The objective is to create a unique and personalized interface, distinct from a standard LLM interaction like ChatGPT4.

### Accomplishments
During this week, I achieved the following milestones:

### 1. ZeroWidth Account Setup
I successfully set up my ZeroWidth account, which will be a crucial component for creating the knowledge interface. ZeroWidth is a valuable platform for hosting static web pages and content. This step enables me to start building and testing the interactive portfolio.

### 2. Attended Design Lecture
I attended a design lecture that focused on using large language models for speech interaction design. The lecture provided valuable insights into the practical applications of LLMs, which will be useful in shaping the knowledge interface.

### 3. Initial Knowledge Context
I began organizing and structuring the knowledge context for my project. This involved selecting and curating relevant information from my course journal and other resources. I made decisions on what information to include and how to present it for a more personalized interaction.

## Next Steps
In the upcoming week, I plan to:

- Continue refining the knowledge context and making informed choices about what to include.
- Start building the interactive portfolio using the ZeroWidth platform.
- Experiment with LLM interaction to create a conversational and engaging user experience.
- Seek feedback from peers and mentors to ensure that the project aligns with its objectives.

## Conclusion
This week has been a productive start to the "Mini Me" project. I am excited to see the knowledge interface take shape and provide a unique and personalized experience for users. As I move forward, I will continue to learn and adapt to the challenges of designing with LLMs, striving for a seamless and meaningful interaction.


## Week Eight report
![logic](PIC/week8/fenmian.jpg)

This week marks the conclusion of the second assignment. Here, I would like to briefly summarize our group project and my takeaways from this topic. The primary objective of this assignment was to acquire proficiency in using Photon and facilitating communication and interaction between multiple Photon boards. Additionally, it aimed to enhance our teamwork skills as we collaborated to create an engaging concept and prototype together.

### Project Background: 
We aim to create a virtual garden for each user by gathering data related to plants and their surroundings, including soil moisture and sunlight information. This data is collected and processed, and then a personalized virtual garden is constructed using Unity. This virtual garden serves as a platform for users to generate and record information about the plants they encounter.
![logic](PIC/week8/background.jpg)

### My work
![logic](PIC/week8/photin.jpg)
In this week's work, my primary responsibilities included connecting and coding for the sensor-related Photon boards, as well as building the casing. Throughout this process, I found that it is quite similar to programming with the Arduino IDE for the most part. When connecting two different Photon boards, both boards need to be under the same account's permissions. The process of uploading code involves the first board uploading to the Photon cloud, and the second board reading from it. When considering how to establish communication between the two boards, I realized that since the first board has a soil moisture sensor and needs to be inserted into the soil for data collection, having it connected to a computer would be quite inconvenient. Therefore, the second board essentially serves as an information relay for future users to place in their homes, used for reading the collected data.

I also used a Falcon structure to create a wooden box because laser cutting is the quickest method for constructing a model. 
![logic](PIC/week8/实拍.jpg)
We then placed the circuit boards inside the box and proceeded to the next phase of testing. Meanwhile, my teammate focused on the Unity part. We integrated the data collected by the Photon into Unity and implemented the corresponding interactions.
![logic](PIC/week8/shiwu.jpg)
![logic](PIC/week8/植物.jpg)

In the course of this project, I gained valuable experience and insights. As part of my responsibilities, I worked on connecting and coding for the sensor-related Photon boards, as well as constructing an efficient wooden casing for the project. Here are some key takeaways and points to consider when working with Photon:

### Takeaways:
1. Similarity to Arduino: Working with Photon boards was akin to working with Arduino, which made the programming aspects familiar and manageable.
2. Account Permissions: When connecting two different Photon boards, it is essential that both boards have the necessary permissions within the same account for smooth code transfer.
3. Data Relay: One board, serving as an information relay (the second board in our case), can be a practical solution when a sensor-equipped board needs to be placed in a location that's inconvenient for direct connections (e.g., in the soil).
4. Efficiency with Laser Cutting: Utilizing laser cutting for constructing the wooden casing proved to be a rapid and efficient method for creating a suitable housing for the project.
5. Unity Integration: Team collaboration was vital as my teammate handled the Unity integration, which involved linking the data collected by the Photon boards to Unity and developing interactive features for the virtual garden.

In summary, this project provided a hands-on opportunity to work with Photon boards and develop a virtual garden that gathers plant-related data. The experience highlighted the importance of teamwork, familiarized me with Photon and Arduino-like programming, and emphasized efficiency in design and testing. To excel in projects like this, it's essential to prioritize user experience, data security, and scalability.

## Week Seven report
![logic](PIC/Snipaste_2023-10-12_14-19-31.jpg)

This week, my team and I had an in-depth discussion to further specify the types of information we need to collect. Our main focus includes soil moisture, sunlight intensity, temperature, and other related image data. This discussion helped us better plan and execute our project.

In this week's work, I took on the task of writing Photon code and was responsible for connecting the circuit board. This part of the work is crucial to ensure that we can successfully collect sensor data. We need to ensure that this data can be transmitted accurately and efficiently to our system.
![logic](PIC/unitysensor.jpg)
Additionally, one of my team members proposed an interesting idea of outputting the collected information into Unity as images. This will add an exciting dimension to our project, allowing us to visualize and analyze the collected data more intuitively. This innovative approach will help us better understand and communicate our research findings.

Overall, this week we made significant progress in the discussion of information types and task assignments. I look forward to continuing our collaboration in future work to turn these ideas into reality and achieve our project goals.

## Week Six report
This week, we conducted a pitch for our group and sparked inspiration. I found my other three team members, and our main focus is on creating wearable technology for plant information collection. After several discussions, we finalized our design background, design objectives, and implementation methods.
## Week Five report

### Overview

During this week, our focus was primarily directed towards becoming more acquainted with the intricate settings of Photon, our primary tool for development. Simultaneously, we dedicated substantial time to the process of brainstorming, as we sought to cultivate ideas and concepts for our second major project, which is on the horizon.

### Project Proposal: "Apocalypse Design"

Among the various proposals put forth, one concept that stood out was my suggestion of "Apocalypse Design." This innovative idea revolves around the integration of wearable technology, the principles of plant biology, the utilization of cutting-edge bio-materials, and the collection of crucial environmental data.

The intriguing nature of "Apocalypse Design" piqued the curiosity and enthusiasm of our entire team. We collectively recognized the immense potential it holds for addressing pertinent issues and challenges in today's world. The prospect of transforming this concept into a tangible reality ignited a spark of excitement among us.

### The Road Ahead

We view this upcoming project as an exhilarating adventure that will undoubtedly push the boundaries of our knowledge and creativity. As we prepare to embark on this journey, we eagerly anticipate the challenges it will present and the innovative solutions we will craft.

In the weeks to come, we plan to share more comprehensive details and updates about the progress of this project, so be sure to stay tuned for the exciting developments on the horizon!

## Week Four report
This weekend, I organized the relevant materials for my first project. Primarily, I wrote a reflective report to articulate my thoughts on the process and the insights gained from my first project. I also discussed the implications of parametric design and computer-aided design for designers, citizens, and the future. During this process, I reflected on my experience and takeaways from using Grasshopper as my first parametric design tool.

Subsequently, I received the topic for my next assignment. I found it quite similar to the open-source hardware projects I've worked on before. I was pleased to receive a kit, as I didn't bring my Arduino-related components with me to the United States. Following the instructions, I registered an account, connected to Wi-Fi, configured the kit, and prepared for Thursday's class.
![logic](PIC/week4/Snipaste_2023-09-21_12-04-57.jpg)
![logic](PIC/week4/Snipaste_2023-09-21_12-08-18.jpg)
![logic](PIC/week4/Snipaste_2023-09-21_12-09-22.jpg)
![logic](PIC/week4/Snipaste_2023-09-21_12-09-54.jpg)


## Week Three report
This week, I continued to deepen my Grasshopper model.
![logic](PIC/Week3/Snipaste_2023-09-13_20-39-32.jpg)
This is my mobile phone stand design. I have experience with Rhino but have hardly used Grasshopper. I'm pleased to have learned the basics of parametric design through small-scale modeling in my daily work.

First, based on the existing mobile phone stand, I studied my phone usage habits. In the office environment, I often place my phone vertically, and in this position, the charging cable is prone to bending and damage. So, in my design, I added a space underneath and a charging cable opening. The specific implementation involved creating a cylinder slightly larger than the charging cable, rotating it using a plane, and then boolean subtracting it from the base.

Additionally, I wanted my model to have an organic surface texture. I tried two types of textures: one with intersecting circular perforations and the other with an eggshell-like texture.
![logic](PIC/Week3/Snipaste_2023-09-14_14-36-17.jpg)
and ultimately chose the fractured eggshell-like texture. To achieve this, I first created a rectangular frame of the appropriate area and randomly placed points inside it. We can adjust randomness by changing the seed. 
![logic](PIC/Week3/Snipaste_2023-09-11_21-22-39.jpg)
Then, I used a powerful tool called "Voronoi," which divided the irregular polygons like cell walls. I scaled these polygons inward using the "scale" tool, ensuring even spacing between different polygons, and then extruded the polygons. This texture was thus created.
![logic](PIC/Week3/Snipaste_2023-09-11_21-22-50.jpg)

For the final touches on the model, I returned to Rhino for surface flow, even though I didn't directly use Grasshopper. During this process, I learned the importance of analyzing the UV surface. By checking the direction of the UV surface's axis, I could ensure a consistent orientation, avoiding random results in the surface flow every time.
![logic](PIC/Week3/Snipaste_2023-09-11_21-51-42.jpg)

In the end, this is my model. I faced a few failed 3D printing attempts, 
![logic](PIC/Week3/397611694494657_.pic.jpg)
but eventually, I achieved this model.


![logic](PIC/Week3/DSC09628.jpg)
![logic](PIC/Week3/DSC09624.jpg)
![logic](PIC/Week3/DSC09620.jpg)
## Week Two report
This week I will adjust more about this cell phone stand
first i will follow the youtube video to have more precise idea about grasshopper

### there are questions about particular battery
1. the negative battery： a negative height （"Negative" is typically used to represent negative values or to indicate numbers that are less than zero in mathematical calculations.）
2. domain button："Domain" container typically includes two values, indicating the lower and upper bounds of the range. This is useful for defining the valid range of values for parameters, ensuring that models or designs stay within reasonable bounds during variations.
3. void button：A "Void" is typically a user-defined function or component. It is often used to represent a virtual or a placeholder element that doesn't directly perform any specific actions.
4. gravity and logic（the most interesting part in the video）
![logic](PIC/Week2/bluered.jpg)
This part primarily projects the center of the entire object onto the tabletop, then compares the center with the center of gravity, using logical judgment to provide a color indication. This makes it convenient for designers to adjust parameters while having an early understanding of the model's stability.
### About battery
I found a chinese booklet for the basic grasshopper booklet
[booklet_pdf](https://jinjieming.com/wp-content/uploads/2016/02/Grasshopper%E5%AE%8C%E5%85%A8%E5%AD%A6%E4%B9%A0%E6%89%8B%E5%86%8CV1.0.pdf)

### Revising process
After making adjustments to the phone's angle, wall thickness, and other aspects based on the video, I discovered that there could be a small storage space in the front of the phone stand for keeping keys, and the bottom could serve as storage while also allowing vertical charging for the phone.
![phone stand](PIC/Week2/chargerhole.jpg)
![phone stand](PIC/Week2/cellphonestand2.jpg)

adding a charging hole
1. first make a cylinder as the charger line
   ![phone stand](PIC/Week2/cellphonestand1.jpg)
3. generate a surface
   ![phone stand](PIC/Week2/rotate.jpg)
5. rotate the cylinder
6. ![phone stand](PIC/Week2/rotate2.jpg)
7. adujust the cylinder
8. stiff
   ![phone stand](PIC/Week2/stiff.jpg)
10. bake

![phone stand](PIC/Week2/rendering1.jpg)
![phone stand](PIC/Week2/rendering2.jpg)
![phone stand](PIC/Week2/rendering3.jpg)

Thank you for the simple version youtube video, it really helps!! 😊😊😊😊😊😊

## Week one report

### Breif：
This week, I have completed the adjustments, output, and laser cutting process for existing files in Rhino and Grasshopper.
![phone stand](PIC/IMG_6572.jpeg)

I first measured: 1. The length and width of my phone. 2. The approximate angle and height when I'm maintaining a normal distance from the table.
![phone stand](PIC/IMG_6570.jpeg)
![phone stand](PIC/IMG_6571.jpeg)

I became familiar with the Grasshopper file and the corresponding adjustable sliders.
![phone stand](PIC/Snipaste_2023-08-31_13-24-17.jpg)
![phone stand](PIC/Snipaste_2023-08-31_14-32-06.jpg)
![phone stand](PIC/studen他.jpg)

I exported a file in AI format. It's important to ensure that:
1. The AI file is in RGB mode.
2. The string in AI: 0.001 inch = 0.072 pt red: cutting, black: non-penetrating.   

I selected a piece of wood and measured its thickness. Then, I adjusted the material, parameters, and starting point on the red laser cutting machine. Afterward, I initiated the cutting process. The entire procedure went smoothly, and soon my piece of wood was cut precisely as needed.
![phone stand](PIC/IMG_6402.jpeg)

Later, I assembled my phone stand and attempted to place my phone on it. I compared my standing position, opened the front-facing camera, and checked if the ergonomics were suitable for comfortable use.
![phone stand](PIC/IMG_6580.jpeg)

I believe that by pre-measuring the individual dimensions and requirements of each person and using the same file to modify parameters for the battery assembly, it's possible to rapidly generate various files and engage in the intriguing process of fabrication. Throughout this process, I consider the utilization of computer-based measurements and AI assistance to be a trend that enables the mass production of customized products with greater efficiency and scalability.
