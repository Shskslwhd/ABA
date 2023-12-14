import cv2
import mediapipe as mp
import pyautogui
import time
import connector as cnt


mp_drawing = mp.solutions.drawing_utils
mp_hands = mp.solutions.hands
##################################
tipIds = [4, 8, 12, 16, 20]
state = None
Gesture = None
wCam, hCam = 720, 640
############################
def fingerPosition(image, handNo=0):
    lmList = []
    if results.multi_hand_landmarks:
        myHand = results.multi_hand_landmarks[handNo]
        for id, lm in enumerate(myHand.landmark):
            # print(id,lm)
            h, w, c = image.shape
            cx, cy = int(lm.x * w), int(lm.y * h)
            lmList.append([id, cx, cy])
    return lmList
# For webcam input:
cap = cv2.VideoCapture(0)
cap.set(3, wCam)
cap.set(4, hCam)
with mp_hands.Hands(
    min_detection_confidence=0.8,
    min_tracking_confidence=0.5) as hands:
  while cap.isOpened():
    success, image = cap.read()
    if not success:
        print("Ignoring empty camera frame.")
      # If loading a video, use 'break' instead of 'continue'.
        continue
    # Flip the image horizontally for a later selfie-view display, and convert
    # конвертирование BGR формата в RGB
    image = cv2.cvtColor(cv2.flip(image, 1), cv2.COLOR_BGR2RGB)
    image.flags.writeable = False
    results = hands.process(image)
    # рисуем аннотацию к управлению жестами на картинке
    image.flags.writeable = True
    image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
    if results.multi_hand_landmarks:
      for hand_landmarks in results.multi_hand_landmarks:
        mp_drawing.draw_landmarks(
            image, hand_landmarks, mp_hands.HAND_CONNECTIONS)
    lmList = fingerPosition(image)
    #print(lmList)
    if len(lmList) != 0:
        fingers = []
        for id in range(1, 5):
            if lmList[tipIds[id]][2] < lmList[tipIds[id] - 2][2]:
                #state = "Play"
                fingers.append(1)
            if (lmList[tipIds[id]][2] > lmList[tipIds[id] - 2][2] ):
               # state = "Pause"
               # pyautogui.press('space')
               # print("Space")
                fingers.append(0)
        totalFingers = fingers.count(1)
        print(totalFingers)
        #print(lmList[9][2])

        if totalFingers == 4:
            state = "Play"
            
            
           # fingers.append(1)
        if totalFingers == 0 and state == "Play":
            state = "Pause"
            pyautogui.press('space')
            print("Space")
            time.sleep(2.0)

            mp_draw=mp.solutions.drawing_utils
            mp_hand=mp.solutions.hands

            tipIds=[4,8,12,16,20]

            video=cv2.VideoCapture(0)

            with mp_hand.Hands(min_detection_confidence=0.5,
                           min_tracking_confidence=0.5) as hands:
                while True:
                    ret,image=video.read()
                    image=cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
                    image.flags.writeable=False
                    results=hands.process(image)
                    image.flags.writeable=True
                    image=cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
                    lmList=[]
                    if results.multi_hand_landmarks:
                        for hand_landmark in results.multi_hand_landmarks:
                            myHands=results.multi_hand_landmarks[0]
                            for id, lm in enumerate(myHands.landmark):
                                h,w,c=image.shape
                                cx,cy= int(lm.x*w), int(lm.y*h)
                                lmList.append([id,cx,cy])
                            mp_draw.draw_landmarks(image, hand_landmark, mp_hand.HAND_CONNECTIONS)
                    fingers=[]
                    if len(lmList)!=0:
                        if lmList[tipIds[0]][1] > lmList[tipIds[0]-1][1]:
                            fingers.append(1)
                        else:
                            fingers.append(0)
                        for id in range(1,5):
                            if lmList[tipIds[id]][2] < lmList[tipIds[id]-2][2]:
                                fingers.append(1)
                            else:
                                fingers.append(0)
                        total=fingers.count(1)
                        cnt.led(total)
                        if total==0:
                            cv2.rectangle(image, (20, 300), (270, 425), (0, 255, 0), cv2.FILLED)
                            cv2.putText(image, "0", (45, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                            cv2.putText(image, "LED", (100, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                        elif total==1:
                            cv2.rectangle(image, (20, 300), (270, 425), (0, 255, 0), cv2.FILLED)
                            cv2.putText(image, "1", (45, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                            cv2.putText(image, "LED", (100, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                        elif total==2:
                            cv2.rectangle(image, (20, 300), (270, 425), (0, 255, 0), cv2.FILLED)
                            cv2.putText(image, "2", (45, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                            cv2.putText(image, "LED", (100, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                        elif total==3:
                            cv2.rectangle(image, (20, 300), (270, 425), (0, 255, 0), cv2.FILLED)
                            cv2.putText(image, "3", (45, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                            cv2.putText(image, "LED", (100, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                        elif total==4:
                            cv2.rectangle(image, (20, 300), (270, 425), (0, 255, 0), cv2.FILLED)
                            cv2.putText(image, "4", (45, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                            cv2.putText(image, "LED", (100, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                        elif total==5:
                            cv2.rectangle(image, (20, 300), (270, 425), (0, 255, 0), cv2.FILLED)
                            cv2.putText(image, "5", (45, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                            cv2.putText(image, "LED", (100, 375), cv2.FONT_HERSHEY_SIMPLEX,
                                2, (255, 0, 0), 5)
                    cv2.imshow("Frame",image)
                    k=cv2.waitKey(1)
                    if k==ord('q'):
                        break
            video.release()
            cv2.destroyAllWindows()

            
        if totalFingers == 1:
            if lmList[8][1]<300:
                print("left")
                pyautogui.press('left')
            if lmList[8][1]>400:
                print("Right")
                pyautogui.press('Right')
        if totalFingers == 2:
            if lmList[9][2] < 210:
                print("Up")
                pyautogui.press('Up')
            if lmList[9][2] > 230:
                print("Down")
                pyautogui.press('Down')
    #cv2.putText(image, str("Gesture"), (10,40), cv2.FONT_HERSHEY_SIMPLEX,
     #              1, (255, 0, 0), 2)
    cv2.imshow("Media Controller", image)
    key = cv2.waitKey(1) & 0xFF
    # если нажата клавиша `q`, то осуществляем выход из цикла
    if key == ord("q"):
        break
  cv2.destroyAllWindows()
