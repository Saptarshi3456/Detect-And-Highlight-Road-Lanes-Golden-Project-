# Detect-And-Highlight-Road-Lanes-Golden-Project-
Write a python code to develop an intelligent system that can detect and highlight road lanes in a video stream, aiding in autonomous vehicle navigation or driver assistance systems.

import cv2
import numpy as np

# Capture video
cap = cv2.VideoCapture('video.mp4')

while True:
    ret, frame = cap.read()
    if not ret:
        break

    # Convert to grayscale and detect edges
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    edges = cv2.Canny(gray, 100, 200)

    # Detect lines using Hough transform
    lines = cv2.HoughLinesP(edges, 1, np.pi/180, 200, minLineLength=100, maxLineGap=10)

    # Filter lines based on slope and length
    left_lane = []
    right_lane = []
    for line in lines:
        x1, y1, x2, y2 = line[0]
        slope = (y2 - y1) / (x2 - x1)
        if slope > 0.5 and slope < 1.5:  # Filter out non-lane lines
            if x1 < frame.shape[1] / 2:  # Left lane
                left_lane.append(line)
            else:  # Right lane
                right_lane.append(line)

    # Draw highlighted lanes
    cv2.line(frame, (left_lane[0][0], left_lane[0][1]), (left_lane[0][2], left_lane[0][3]), (0, 255, 0), 2)
    cv2.line(frame, (right_lane[0][0], right_lane[0][1]), (right_lane[0][2], right_lane[0][3]), (0, 255, 0), 2)

    # Display output
    cv2.imshow('Lane Detection', frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
