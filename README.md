## Requirements for program
To run the program you required to have openCV, numpy, dlib, imutils(face_utils specifically).
<br>
## How the code Works?: 

In simple words, code tries to measure the ratio of vertical and horizontal distance of your eyes. If that ratio lies between certain range(obviously based on previous data) one can predict if the eye is open or slightly open or closed. We first detect a face using dlib's frontal face detector. Once the face is detected , we try to detect the facial landmarks in the face using the dlib's landmark predictor. The landmark predictor returns 68 (x, y) coordinates representing different regions of the face, namely - mouth, left eyebrow, right eyebrow, right eye, left eye, nose and jaw. Ofcourse, we don't need all the landmarks, here we need to extract only the eye region. 

Here are those 68 landmarks that dlib uses:
<a href= "https://pyimagesearch.com/wp-content/uploads/2017/04/facial_landmarks_68markup.jpg">Please Note the landmark number for eye</a>
<img src = "https://pyimagesearch.com/wp-content/uploads/2017/04/facial_landmarks_68markup.jpg">
  ```python
  detector = dlib.get_frontal_face_detector()
predictor = dlib.shape_predictor("shape_predictor_68_face_landmarks.dat")
```
  The code starts by Initializing the camera and taking the instance of the subject and then it Initialize the dlib's face detector and landmark detector.  
  Then by looping in above mentioned face detector's generated rectangle, we can find the (x,y) co-ordinates of the rectangle under which whole faace can be located.
  See below code:
```python
  faces = detector(image)
    #detected face in faces array
    for face in faces:
        x1 = face.left()
        y1 = face.top()
        x2 = face.right()
        y2 = face.bottom()

        face_frame = frame.copy()
        cv2.rectangle(face_frame, (x1, y1), (x2, y2), (0, 255, 0), 2)
```
  Then we have also located landmarks on face and converted them to an array using numpy.
  ```python
  position = predictor(image, face)
        position = face_utils.shape_to_np(position)
```
  
  
The below function calculate that ratio of eye in open or closed state, by that ratio we can predict whether eye is open or closed.
You can also change the values accordingly to you.
```python 
def stat(a,b,c,d,e,f):
	vtl = calc(b,d) + calc(c,e)
	htl = calc(a,f)
	ratio = vtl/htl

	if(ratio>0.42 and ratio<=0.50):
		return 1
	elif(ratio>0.50):
		return 2
	else:
		return 0
```
<br>
The code below uses above function in action, see how the arguments are passed, thosed 68 facial landmarks are stored in form of array and those related to eyes are only used in the code below.
```python 
right_eye = stat(position[42],position[43], 
        	position[44], position[47], position[46], position[45])
        left_eye = stat(position[36],position[37], 
        	position[38], position[41], position[40], position[39])
```
In the last part of the code we have to program only when to show different states accordingly to the ratio we received as described above:
<br> 
```python 
if(right_eye==1 or left_eye==1):
        	active=0
        	drowsy+=1
        	sleep=0
        	if(drowsy>6):
        		status="Drowsy!"
        		color = (0,0,255)

        elif(right_eye==0 or left_eye==0):
        	active=0
        	drowsy=0
        	sleep+=1
        	if(sleep>6):
        		status="SLEEPING!!!"
        		color = (255,0,0)

        else:
        	active+=1
        	drowsy=0
        	sleep=0
        	if(active>6):
        		status="Active!"
        		color = (0,255,0)
```

