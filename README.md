# Shape-detection
Shape detection and contour tracing on object
import cv2
import numpy as np


# we define a function to get contours
def getCountours(img):
    contours,hierarchy = cv2.findContours(img, cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_NONE)
    # RETR_External extracts the outer countour
    # Ther are other countour commands to find all contours but for outer countour use this
    #cv2.chain contour will give all contour points (this will give contour values
    i = 0
    for cnt in contours:
        area = cv2.contourArea(cnt)

                # give a threshold to it does not detect any noise data
        if area>100:
            i = i + 1
            cv2.drawContours(imgContour, cnt, -1, (255, 0, 0), 3)
            # cnt for image we want to draw. imgcontour original image to draw contour on, -1 as we want to draw on same image
            print("area of ", i, " image is ", area)
            peri = cv2.arcLength(cnt,True) #calculate the perimeter
            print("Perimeter of",i,"image is", peri)

            # finding corner point  (approximately) to find shape of image, if corner is more than 4, it is a circle
            approx = cv2.approxPolyDP(cnt, 0.1*peri, True)
            # epsilon is for resolution (can play around with if not getting good results (0.1 (something * peri means what length is edge
            # , True for closed curves
            #print("Point matrix with location detail", approx) # this is the command
            # print number of edges to get idea of shape
            print(len(approx))
            objcor = len(approx)
            # making a boundary box around the object, x, y, width, height
            x,y,w,h = cv2.boundingRect(approx)

            if objcor ==3: objecType ="Tri"
            elif objcor == 4 :
                aspRatio = w/float(h)
                if aspRatio >0.95 and aspRatio <1.05 : objecType ="Square"
                else:objecType = "Rectangle"
            elif objcor >4: objecType = "circle"
            else: objecType = "xxxxxxxx"

            cv2.rectangle(imgContour, (x,y),(x+w,y+h),(0,255,0),2)
            # from bounding boxes we can get height, length and centre location of the object
            cv2.putText(imgContour, objecType,(x+(w//2)-10,y+(h//2)-10),cv2.FONT_HERSHEY_COMPLEX,0.3,(0,0,0),2)
            #now where to print(centre of object slight offset





path = 'resources/shapes pics.PNG'
img = cv2.imread(path)
imgContour = img.copy() # to copy the "img" file

# we are going to preprocess the image, cnvert to gray scale and then find edges to find corner point
imgGray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)

#apply image blur
imgBlur = cv2.GaussianBlur(imgGray, (3,3),1) # 3x3 blur , sigma of 1 higher sigma more blur

# Canny edge detector to find edges
imgCanny = cv2.Canny(imgBlur, 50,100)

# call the contour function
getCountours(imgCanny)

# joining gray images
imghor= np.hstack((imgGray,imgBlur, imgCanny))
imghor2= np.hstack((img,imgContour))


'''
cv2.imshow("original",img) # this is original image
cv2.imshow("Gray",imgGray)
cv2.imshow("Blur",imgBlur)
cv2.imshow("Blur",imgBlur)
cv2.imshow("Joined",imghor)
'''
cv2.imshow("contour image", imghor2)
cv2.waitKey(0)
