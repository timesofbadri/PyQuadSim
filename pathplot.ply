#! /usr/bin/python

'''
pathplot.py - Simple path plot using OpenCV.  Tests with random walk.

Copyright (C) 2014 Simon D. Levy

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Lesser General Public License as 
published by the Free Software Foundation, either version 3 of the 
License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU Lesser General Public License 
along with this code.  If not, see <http://www.gnu.org/licenses/>.
'''

from random import Random
from math import sin, cos
import sys
import cv

class PathPlotter:
    '''
    A class for X/Y plots.
    '''

    def __init__(self, display_size, lower, upper, title='X/Y', pause_msec=1, color_rgb=(255,255,0)):
        '''
        Creates an XYPlot object with the following inputs:

        display_size     - size of square display in pixels
        lower            - coordinates of lower-left corner point
        upper            - coordinates of upper-right corner point

        title        - optional window title
        pause_msec   - optional millisecond pause between updates
        color_rgb    - optional color 
        '''

        # Create a window and image for display
        cv.NamedWindow(title, 1 )
        self.image = cv.CreateImage((display_size, display_size), cv.IPL_DEPTH_8U, 3)
 
        # Store stuff for later
        self.title = title
        self.lower = lower
        self.upper = upper
        self.display_size = display_size
        self.pause_msec = pause_msec

        # OpenCV uses BGR instead of RGB, so convert
        self.actual_bgr = self._rgb2bgr(color_rgb)

        # Initialize trajectory
        self.trajectory = []

    def plot(self, actual_pose):
        '''
        Plots pose and trajectory.
        '''

        # Clear out old image
        cv.Set(self.image, (0,0,0))

        # Add actual pose
        self._add_pose(actual_pose, self.actual_bgr)
 
        # Display the new image
        cv.ShowImage(self.title, self.image)

        # Pause and return False if ESC hit, True otherwise
        return cv.WaitKey(self.pause_msec) != 27

    def _add_pose(self, pose, bgr):

        # Add pixel coordinates to trajectory
        self.trajectory.append((pose[0], pose[1]))

        # Plot trajectory
        for k in range(1, len(self.trajectory)):

            x, y         =  self._coords2pixels(self.trajectory[k])
            xprev, yprev = self._coords2pixels(self.trajectory[k-1])

            cv.Line(self.image, (xprev, yprev), (x, y), bgr)

        # Get a polyline (e.g. triangle) to represent the pose icon
        pose_points = self._pose_polyline()
                               
        # Rotate the polyline by the current angle
        pose_points = map(lambda pt: self._rotate(pt, pose[2]), pose_points)
                                                                                               
        # Move the polyline to the current pose position
        curr = self._coords2pixels(self.trajectory[-1])
        pose_points = map(lambda pt: (int(curr[0]+pt[0]), int(curr[1]+pt[1])), pose_points)

        # Add an icon for the pose
        cv.FillPoly(self.image, [pose_points], bgr)

    def _rotate(self, pt, theta):
        c = cos(theta)
        s = sin(theta)
        x,y = pt  
        return int(x*c - y*s), int(x*s + y*c)
 
    def _pose_polyline(self):
        SIZE = 10
        xlft = -SIZE / 2
        xrgt =  SIZE / 2
        ybot =  SIZE/1.25 / 2
        ytop = -SIZE / 2
        return [(xlft,ybot), (xrgt,0), (xlft,ytop)]
 
    def _rgb2bgr(self, rgb):

        return rgb[2], rgb[1], rgb[0]

    def _coords2pixels(self, coords):

        return self._coord2pix(coords, 0, +1), self._coord2pix(coords, 1, -1)

    def _coord2pix(self, coords, idx, sgn):

        coord = sgn*coords[idx] - self.trajectory[0][idx]

        coordspan  = (self.upper[idx] - self.lower[idx]) / 2

        pixspan = self.display_size / 2 

        return int(pixspan + pixspan * coord/coordspan)

# Test =========================================================================================

if __name__ == "__main__":


    TURN = 0.5

    plotter = PathPlotter(600, (-50, -50), (+50,+50), title='Random Walk', pause_msec=100)

    # x, y, theta
    pose = 0,0,0

    rand = Random()

    while True:

       theta = pose[2]

       pose = pose[0] + cos(theta), pose[1] - sin(theta), theta

       if not plotter.plot(pose):
            break

       pose = pose[0], pose[1], rand.gauss(theta, TURN)

 

