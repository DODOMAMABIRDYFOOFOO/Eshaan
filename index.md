# Smart Glasses
This is a glasses that integrates gemini to solve hard math problems along with describing what it is looking at. It is actvated through an app that uses voice assistant to help them controls what command you want to input. Along with that it uses an OLED screen to display necessary information. 

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Eshaan R | Sacred Heart Middle School | Electrical Engineering | Incoming 8th Grader


![Headstone Image](EshaanR.png)


# Third Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/VRiodOMrIRw?si=6pVeFEuWR9AvuW78" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
In my third milestone I was able to integrate gemini into my code along with adding text to speech to announce gemini's response. While I was working with gemini I had to create 3 accounts with different API Keys because I kept overusing gemini and reached about 150 requests per day. Throughout my third milestone I faced many challenges inculding the text to speech not speaking in english along with the speaker not being connnected to the raspberry pi. In order to solve these problems I had to run numerous commands in the terminal. For my next milestone I will make the text to speech voice less choppy, add an app that can be connected to my glasses, along with adding a OLED screen that displays necessary information. 

**Code**

This is the code for the gemini integration:  
```c++

import cv2
import time
from picamera2 import Picamera2
import google.generativeai as genai
import subprocess

# Configure your Google API key
genai.configure(api_key="AIzaSyDqy4MH05pQtURu17abpys3fQ9Ju0uG6RQ")

model = genai.GenerativeModel('gemini-1.5-flash')
 
# Test API connection
print("Testing API...")
try:
    test_response = model.generate_content("Hello test")
    print(f" API test successful: {test_response.text}")
except Exception as e:
    print(f" API test failed: {e}")
    exit(1)

picam2 = Picamera2()
picam2.preview_configuration.main.size = (640, 480)
picam2.preview_configuration.main.format = "RGB888"
picam2.configure("preview")
picam2.start()

def speak_text(text):
    print(f" Speaking: {text}")
    # Smooth male voice settings
    subprocess.run([
        'espeak', 
        '-v', 'en+m2',      # Male voice variant 2
        '-s', '200',        # Slower speed for smoother speech
        '-p', '78',         # Lower pitch for deeper male voice
        '-a', '100',        # Full volume
        '-g', '1',         # Longer gaps between words (reduces choppiness)
        '-k', '5',          # Capital letter indication (softer)
        '--stdin'           # Better processing for longer text
    ], input=text, text=True)

def capture_image(path="/tmp/gemini_frame.jpg"):
    frame = picam2.capture_array()
    cv2.imwrite(path, frame)
    print(f" Image saved to {path}")
    return path

def analyze_with_gemini(image_path, prompt="Describe the image"):
    print(f"Reading image from {image_path}")
    try:
        with open(image_path, "rb") as img_file:
            image_data = img_file.read()
        
        print(f"Image size: {len(image_data)} bytes")
        
        response = model.generate_content([
            prompt,
            {
                "mime_type": "image/jpeg",
                "data": image_data
            }
        ])
        return response.text
    except Exception as e:
        print(f" Gemini analysis failed: {e}")
        return f"Error analyzing image: {e}"

try:
    while True:
        print("\n" + "="*50)
        print("Capturing frame...")
        img_path = capture_image()

        print("Sending to Gemini...")
        result = analyze_with_gemini(img_path, 
            "If there is a math problem being shown analyze the question and then give the answer to the question don't just say the question without any answer, if it is multiple choice then look at all the answers and based on that give me an answer, remember to look at the problem carefully, also when talking don't say the symbols just say the words and only words if given problems without solutions then still solve it, remeber that if the question is a history or a reading comprehension or anything that is in a school curriculum then just solve the problems don't put in /sub/sub * and anything that has symbols give me a straight up response don't put any symbols in the response just the answer; if there is no math problem then say what is happening but at the end don't say there is no math problem present")

        print("Gemini says:")
        print(result)

        speak_text(result)
        print("⏱️  Waiting 2 second...")
        time.sleep(2)

except KeyboardInterrupt:
    picam2.stop()
    print("\n Stopped.")
```

# Second Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/-fj1hPKtbZc?si=z_3792kE-EdAotwj" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In my second milestone I was able to attach the camera to my glasses, and create a ultrasonic sensor to go along with the attachments. While attaching a camera may sound like a simple job, it wasn't. When I attached the camera to the 3d printed model I created(picture is below), it disrupted a metal piece on my camera which made it not functional. In order to fix this I got a new camera and this time did not repeat the mistake of adding a 3d-printed cover to the lenses. The ultrasonic sensor, on the other hand, was a lot easier since I am extremely familiar with breadboarding and coding these type of snesors. 
At first I expected this project to be mainly AR, but as I started building and building I realized this was more for detecting objects rather than having a screen in front of my eyes. I also thought that making the camera would be easy since I thought that I would get some prebuilt camera that could easily save pictures. 
Throughout this milestone I faced a major setback when my SD card had to be reflashed since my Tiger VNC and SSH weren't functioning properly. So we reflashed the SD card and I had to redownload all of my previous code and code the entire ultrasonic sensor again. Another minor challenge that I faced was my Libcamera and RPI vision modules weren't functioning properly. In order to solve this problem I reinstalled all of the pacakages needed to make Libcamera and RPI-Vision needed. Though I had these to problems the result turned out to be that my SSH and VNC were working properly unlike before and I made the connecting of the wires/cables to my computer much easier. 
In order for me to complete my final milestone I need to have completed the visual design of my glasses which includes 3d printing some frame to hold the long cable that supports the camera along with making the glasses look a lot neater in desgin. I also am planning that start on my modification
s which includes adding vibrator to my ultrasonic sensor, I also decided to use a hat where I can put the bread board along with the raspberry pi. Inorder to put the raspberry pi and the breadboard I will 3d print a compartment for the hat to keep the two items. My third modification will be integrating AI into my glasses so it tells the user what the views are. For example, if they are in switzerland the AI will tell them "There is giant tree 3 feet in front of you and a beautiful lake swarming with life 10 feet infront of you." 

<img width="500" height="300" alt="Screenshot 2025-07-11 at 11 48 22 AM" src="https://github.com/user-attachments/assets/594fc0eb-754f-490f-8b8f-35113b8d90f6" />

This was the desing for where I was keeping the camera, as we can see from the design it has a place for the long cable along with a place for the picamera. 



<img width="250" height="300" alt="Screenshot 2025-07-11 at 11 50 01 AM" src="https://github.com/user-attachments/assets/a7ea8e34-e214-4b2d-937c-690d1222d53b" />

This was the design that failed and that cost me a picamera, as we can see it disrupted the metal thing attached to the camera making it not functional. 

**Schematic Design for breadboard**



<img width="513" height="342" alt="Screenshot 2025-07-11 at 11 55 26 AM" src="https://github.com/user-attachments/assets/86f27d32-8de2-4cac-9a81-42519ed92d9c" />

This is the schematic design for the wiring of the buzzer and the ultrasonic sensor to my raspberry pi. I faced a challenge with not having the right resistors but I found out that it was just the ratio of the power that mattered for 1k:2k was the same as 100K:200K. 


**Code**

This is the code for the ultrasonic sensor. 
```c++

#!/usr/bin/python3
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)


PIN_TRIGGER = 19
PIN_ECHO = 26
PIN_BUZZER = 21


GPIO.setup(PIN_TRIGGER, GPIO.OUT)
GPIO.setup(PIN_ECHO, GPIO.IN)
GPIO.setup(PIN_BUZZER, GPIO.OUT)


GPIO.output(PIN_TRIGGER, GPIO.LOW)
GPIO.output(PIN_BUZZER, GPIO.LOW)


print("Waiting for sensor to settle...")
time.sleep(2)


try:
   while True:
       # === Trigger ultrasonic pulse ===
       GPIO.output(PIN_TRIGGER, GPIO.HIGH)
       time.sleep(0.00001)  # 10 microseconds
       GPIO.output(PIN_TRIGGER, GPIO.LOW)


       # === Wait for echo signal ===
       timeout = time.time() + 1
       while GPIO.input(PIN_ECHO) == 0:
           pulse_start = time.time()
           if pulse_start > timeout:
               print("Timeout: No echo received (start)")
               pulse_start = None
               break


       timeout = time.time() + 1
       while GPIO.input(PIN_ECHO) == 1:
           pulse_end = time.time()
           if pulse_end > timeout:
               print("Timeout: No echo received (end)")
               pulse_end = None
               break


       # === Calculate distance ===
       if 'pulse_start' in locals() and 'pulse_end' in locals() and pulse_start and pulse_end:
           pulse_duration = pulse_end - pulse_start
           distance = round(pulse_duration * 17150, 2)
           print("Distance:", distance, "cm")


           # === Buzzer Logic: Beep rate based on distance ===
           if distance < 1:
               print("Object is DANGEROUSLY close! Fastest beeping...")
               beep_time = 0.01
           elif distance < 3:
               print("Object is EXTREMELY close! Very fast beeping...")
               beep_time = 0.05
           elif distance < 5:
               print("Object is WAY too close! Fast beeping...")
               beep_time = 0.1
           elif distance < 10:
               print("Object is close! Normal beeping...")
               beep_time = 0.2
           elif distance < 100:
               print("Object detected, slow beep.")
               beep_time = 0.5
           else:
               print("Object far or not detected. No beep.")
               beep_time = None


           if beep_time is not None:
               GPIO.output(PIN_BUZZER, GPIO.HIGH)
               time.sleep(beep_time)
               GPIO.output(PIN_BUZZER, GPIO.LOW)
               time.sleep(beep_time)
           else:
               GPIO.output(PIN_BUZZER, GPIO.LOW)
               time.sleep(0.5)


       else:
           print("Could not calculate distance.")
           GPIO.output(PIN_BUZZER, GPIO.LOW)
           time.sleep(0.5)


except KeyboardInterrupt:
   print("\nStopped by user.")
```
This is the code for the picamera: 

```c++
# SPDX-FileCopyrightText: 2021 Andrew Reusch for Adafruit Industries
#
# SPDX-License-Identifier: MIT
import time
import logging
import argparse
import pygame
import os
import sys
import numpy as np
import subprocess
import re


CONFIDENCE_THRESHOLD = 0.5   # at what confidence level do we say we detected a thing
PERSISTANCE_THRESHOLD = 0.25  # what percentage of the time we have to have seen a thing


# App
from rpi_vision.agent.capture import PiCameraStream
from rpi_vision.models.teachablemachine import TeachableMachine


logging.basicConfig()
logging.getLogger().setLevel(logging.INFO)


# initialize the display
pygame.init()
screen = pygame.display.set_mode((0, 0), pygame.FULLSCREEN)


capture_manager = PiCameraStream(resolution=(screen.get_width(), screen.get_height()), preview=False)


def parse_args():
   parser = argparse.ArgumentParser()
   parser.add_argument('--include-top', type=bool,
                       dest='include_top', default=True,
                       help='Include fully-connected layer at the top of the network.')


   parser.add_argument('savedmodel', help='TeachableMachine savedmodel')


   parser.add_argument('--tflite',
                       dest='tflite', action='store_true', default=False,
                       help='Convert base model to TFLite FlatBuffer, then load model into TFLite Python Interpreter')


   parser.add_argument('--rotation', type=int, default=0,
                       help='Rotation for display in degrees (0, 90, 180, 270)')


   args = parser.parse_args()
   return args


last_seen = [None] * 10
last_spoken = None


def validate_target_name(name):
   # Allow only alphanumeric + underscores + spaces + hyphens, no weird chars
   return bool(re.match(r'^[\w\s\-]+$', name.strip()))






def main(args):
   print("\n--- Target Detection Setup ---")
   print("Enter one or more target names to detect, separated by commas.")
   print("Allowed characters: letters, numbers, spaces, underscores, hyphens.")
   print("Example: cat, dog, blue_car")
   print("Type 'help' to show this message again.")
   print("Type 'quit' or 'exit' to stop the program.\n")


   while True:
       user_input = input("Enter target names: ").strip()
       if user_input.lower() in ('quit', 'exit'):
           print("Exiting program.")
           sys.exit(0)
       if user_input.lower() == 'help':
           print("\nAllowed characters: letters, numbers, spaces, underscores, hyphens.")
           print("Example: cat, dog, blue_car\n")
           continue
       if not user_input:
           print("Input cannot be empty. Please enter at least one target name.")
           continue


       # Split by commas and validate each name
       targets = [t.strip() for t in user_input.split(',')]
       invalids = [t for t in targets if not validate_target_name(t)]
       if invalids:
           print(f"Invalid target names detected: {invalids}")
           print("Please use only allowed characters (letters, numbers, spaces, underscores, hyphens).")
           continue
      
       print(f"Targets to detect: {targets}")
       return targets


   global last_spoken, capture_manager


   # Get user input for targets to detect
   targets_to_detect = get_user_targets()
   print("Starting detection for targets:", targets_to_detect)


   capture_manager = PiCameraStream(resolution=(screen.get_width(), screen.get_height()), preview=False)


   if args.rotation in (0, 180):
       buffer = pygame.Surface((screen.get_width(), screen.get_height()))
   else:
       buffer = pygame.Surface((screen.get_height(), screen.get_width()))


   pygame.mouse.set_visible(False)
   screen.fill((0, 0, 0))
   try:
       splash = pygame.image.load(os.path.join(os.path.dirname(sys.argv[0]), 'bchatsplash.bmp'))
       splash = pygame.transform.rotate(splash, args.rotation)
       splash = pygame.transform.scale(splash, (min(screen.get_width(), screen.get_height()), min(screen.get_width(), screen.get_height())))
       screen.blit(splash, ((screen.get_width() - splash.get_width()) // 2, (screen.get_height() - splash.get_height()) // 2))
   except pygame.error:
       pass
   pygame.display.update()


   scale = max(buffer.get_height() // capture_manager.resolution[1], 1)
   scaled_resolution = tuple([x * scale for x in capture_manager.resolution])


   smallfont = pygame.font.Font(None, 24 * scale)
   medfont = pygame.font.Font(None, 36 * scale)
   bigfont = pygame.font.Font(None, 48 * scale)


   # Load your model here; example with MobileNetV2Base if defined
   model = TeachableMachine(args.savedmodel, include_top=args.include_top, tflite=args.tflite)


   capture_manager.start()


   while not capture_manager.stopped:
       for event in pygame.event.get():
           if event.type == pygame.QUIT:
               capture_manager.stop()
               pygame.quit()
               sys.exit(0)
           elif event.type == pygame.KEYDOWN:
               if event.key == pygame.K_ESCAPE:
                   capture_manager.stop()
                   pygame.quit()
                   sys.exit(0)


       if capture_manager.frame is None:
           continue


       buffer.fill((0, 0, 0))
       frame = capture_manager.read()
       previewframe = np.ascontiguousarray(capture_manager.frame)
       img = pygame.image.frombuffer(previewframe, capture_manager.resolution, 'RGB')
       img = pygame.transform.scale(img, scaled_resolution)


       cropped_region = (
           (img.get_width() - buffer.get_width()) // 2,
           (img.get_height() - buffer.get_height()) // 2,
           buffer.get_width(),
           buffer.get_height()
       )
       buffer.blit(img, (0, 0), cropped_region)


       timestamp = time.monotonic()
       prediction = model.tflite_predict(frame)[0] if args.tflite else model.predict(frame)[0]
       delta = time.monotonic() - timestamp


       logging.info(prediction)
       logging.info("%s inference took %d ms, %0.1f FPS" % ("TFLite" if args.tflite else "TF", delta * 1000, 1 / delta))


       # Show all targets on screen at top left
       targets_text = "Targets: " + ", ".join(targets_to_detect)
       targets_surface = medfont.render(targets_text, True, (0, 200, 255))
       buffer.blit(targets_surface, (10, 10))


       fpstext = "%0.1f FPS" % (1 / delta,)
       fpstext_surface = smallfont.render(fpstext, True, (255, 0, 0))
       buffer.blit(fpstext_surface, fpstext_surface.get_rect(topright=(buffer.get_width() - 10, 10)))


       try:
           temp = int(open("/sys/class/thermal/thermal_zone0/temp").read()) / 1000
           temptext = "%d\N{DEGREE SIGN}C" % temp
           temptext_surface = smallfont.render(temptext, True, (255, 0, 0))
           buffer.blit(temptext_surface, temptext_surface.get_rect(topright=(buffer.get_width() - 10, 30)))
       except OSError:
           pass


       detected_any = False


       # Go through predictions and find any target matched above threshold
       for p in prediction:
           label, name, conf = p
           if conf > CONFIDENCE_THRESHOLD and any(name.lower() == target.lower() for target in targets_to_detect):
               detected_any = True


               persistant_obj = False
               last_seen.append(name)
               last_seen.pop(0)
               inferred_times = last_seen.count(name)
               if inferred_times / len(last_seen) > PERSISTANCE_THRESHOLD:
                   persistant_obj = True


               detecttext = name.replace("_", " ")
               for f in (bigfont, medfont, smallfont):
                   detectsize = f.size(detecttext)
                   if detectsize[0] < screen.get_width():
                       detecttextfont = f
                       break
               else:
                   detecttextfont = smallfont


               detecttext_color = (0, 255, 0) if persistant_obj else (255, 255, 255)
               detecttext_surface = detecttextfont.render(detecttext, True, detecttext_color)
               detecttext_position = (buffer.get_width() // 2, buffer.get_height() - detecttextfont.size(detecttext)[1])
               buffer.blit(detecttext_surface, detecttext_surface.get_rect(center=detecttext_position))


               if persistant_obj and last_spoken != detecttext:
                   subprocess.call(f"echo {detecttext} | festival --tts &", shell=True)
                   last_spoken = detecttext
               break


       if not detected_any:
           last_seen.append(None)
           last_seen.pop(0)
           if last_seen.count(None) == len(last_seen):
               last_spoken = None


       screen.blit(pygame.transform.rotate(buffer, args.rotation), (0, 0))
       pygame.display.update()




if __name__ == "__main__":
   args = parse_args()
   try:
       main(args)
   except KeyboardInterrupt:
       capture_manager.stop()

finally:
   GPIO.cleanup()
```
# First Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/CoPw9rdKQRo?si=n4YrnZoTD_a2I4BL" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In my first milestone it was mainly about getting the camera connected to my raspberry pi and to make it work. I was able to make the camera fully functional.  My computer was not being flexible with the raspberry pi that I had connected to it. My goal is to have the camera connected to the glasses and make sure that the display on the glasses show up. 
Throughout my first milestone I encountered errors with my TigerVNC, and my raspberry pi not working. It was most likely something to do with my computer. In order to solve this problem I just had to reboot the coding software and it finally worked. For the Tiger VNC it was a much different story I still wasn't able to connect only to my raspberry pi. Despite these countless errors I was able to complete a working camera that can record for a short period of time. 


# Starter Project

<iframe width="560" height="315" src="https://www.youtube.com/embed/aZOjyn9acaA?si=FDQXHNn1Wcfg0Y0i" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My starter project was a retro arcade using mainly soldering techniques. One large challenge I faced was that I lost more than half the screws so I had to find them in a different room. My next step will be to build my actual project which is smart glasses

Here are the materials 

NO. Name Model/Specification OTY
1 Buzzer 12mm 5V; Passive 1


2 Electric capacitor 220uF 16V 1


Micro USB 4-pin 90 1


4 Power cable for micro-USB: 50cm 1


5 Self-switch BxBmm 1

Self-switch cap red 1

KEY 7 Digitron display 3631AS 1


IC Chip with core board STC8G2K1652 1


g Chip capacitor 10uF 1


POWER 10 LED dot matrix module 1088BS; 8x8mm 2


11 Button 12x12x7.3mm 6


12 Button caр A24 12x12x7.3mm 6


COWTHCONT 13 PCB 7x9.8mm


14 Screw M3x5mm 8


15 Screw M3x8mm 2


16 Copper column Double-pass: M3x9mm


PO ATУTICAKO 2О: 4 САРО


BEEP 17 Hexagonal column Single-head; 5+6mm 4


18 Battery case AAA 1

<img width="450" alt="Screenshot 2025-07-07 at 1 44 57 PM" src="https://github.com/user-attachments/assets/1027c8ef-a276-46bb-bba9-8023b1077bd3" />


# Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
|Glasses with Dark Lense | The glasses itself | $9 | <a href="[https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://www.amazon.com/Simple-Classic-Sunglasses-Polarized-Protection/dp/B0F1B5KJQT/ref=sr_1_10?crid=1XOQMHY4GRJY2&dib=eyJ2IjoiMSJ9.SLSt0x6ZrjeCE-exHSN2SXjmtPjX1IvCP10jr0jexZ0Z_i1H7oHuhdZgy55XIi4GNohUgIhXA9gHiFN2__lFcoLTb2Vw0ifjl0gY7vhjanhowXVSmds6E3hn4_mADfDGpdVur4_g8AvGz8yTDcTPdyC5x4BLfx8h4OVS2uJWxg_5rNjY-EUX2JtsjcWa4ikId9rCMTQLuUn_wR_57PdhmUW2vRxNylo4S-XdWYBqUUIXFaYUX5znLSULrmNgGWlqHmqJW87mbHnbbevrjmXhb5-3ah2neLafNYjFM0hjbzk.HWV8VDlj1UtiWy7FwhN9TOxutfI_T457sdIcK9-9bwg&dib_tag=se&keywords=Black+Matte+Black+Frame%2FGrey+Lens&qid=1752527241&sprefix=black+matte+black+frame%2Fgrey+lens%2Caps%2C245&sr=8-10)"> Link </a> |
| Raspberry Pi 4 + Canakit | It was used for the backend and running my code | $149 | <a href="[https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://www.amazon.com/CanaKit-Raspberry-4GB-Starter-Kit/dp/B08956GVXN/ref=sr_1_3?crid=1B7S6IDV1U9P7&dib=eyJ2IjoiMSJ9.4wZGiZcG7IfVeIs8ylcbr2OWb6dH50gzNdgVi5k-In4fdTS4pr_55N4uBsdWFVWPS5BhQck8BMzB8ZD2cthkas81OPwd8xoSWSHaCHgoxqg4jY8VUPl8TosZZ0d7yrt45pp3fsDvWQ5QnoLD-qiPh-HN9sM_BVIt71hCQhLaLVM7M28ia17ZFshjGdtdhCRcGClQkOP2Y3_dJV1cGUt404kOap5zzNeNLSm8H_6Rjx0.DB-V_-kn9VFOEc3ugst8pdtXd50kgb4ycPrB6DWYcIU&dib_tag=se&keywords=raspberry%2Bpi%2B4&qid=1752594912&sprefix=raspberry%2Bpi%2B4%2Caps%2C196&sr=8-3&th=1)"> Link </a> |
| OLED Screen| Displaying information on the lenses | $28.99 | <a href="https://www.amazon.com/dp/B0B8N46G24?psc=1&smid=A50C560NZEBBE&ref_=chk_typ_imgToDp"> Link </a> |
