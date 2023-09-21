# Qu Xinyu Hiro's Document
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
