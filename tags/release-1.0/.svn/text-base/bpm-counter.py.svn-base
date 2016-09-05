#!/usr/bin/env python
# -*- coding: iso-8859-1 -*-
# curses don't understand utf8 (yet)


# Copyright (C) 2009  H. Dieter Wilhelm
# Author: H. Dieter Wilhelm <dieter@duenenhof-wilhelm.de>
# Created: 2009-01
# Version: 1.0

# This code is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published
# by the Free Software Foundation; either version 3, or (at your
# option) any later version.
#
# This python script is distributed in the hope that it will be
# useful, but WITHOUT ANY WARRANTY; without even the implied warranty
# of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
#
# Permission is granted to distribute copies of this pyhton script
# provided the copyright notice and this permission are preserved in
# all copies.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, you can either send email to this
# program's maintainer or write to: The Free Software Foundation,
# Inc.; 675 Massachusetts Avenue; Cambridge, MA 02139, USA.

# --- TODO ---
# expectation for a 95 % confidence level
# (command line option for) choice of precision
# PrintStatus() not working properly
# Adjust accuracy during run?
# Provide acceleration information?
# TCL/TK, GTK version for our mice lovers and Windows pampered?

"""Timer for counting something regular, like the beats of music.
"""

__author__ = 'dieter@duenenhof-wilhelm.de (Dieter Wilhelm)'

# --- command line help ---

import sys

if len( sys.argv) > 1:
    print """Display the frequency of keystrokes in 1/min (bpm).

    usage: This is self evident ;-).
Version 1.0
""", sys.argv[ 0]
    exit ( 1)

# --- helper functions ---

import math

def mean( A):
    """Mean of the list A."""
    l = len( A)
    return sum( A) / float( l)

def variance( A):
    """Variance (n-1) of the list A.
Return 0 if len( A) < 2.
    """
    m = mean( A)
    l = len( A)
    v = 0
    for x in A:
       v = v + ( x - m)**2
    if l > 1:
        return v / float( l - 1)
    else:
        return 0.

def standardDeviation( A):
    """Standard deviation of the list A."""
    return math.sqrt( variance( A))
    
def movingAverage( A, n = 5):
    """List A's mean of the last n members.

If len( A) < n, return the mean of the less than n elements."""
    return mean( A[ -n:]) 

# --- class definitions ---
    
#from time import *
import time
 # time.clock() is the CPU time!
 # time.time() is the Wall (real world) time!
import string

class StopWatch():
    """Container of points in time measured in s."""
    def __init__( self):
        """Resetting the container."""
        self.times = []
        
    def ClockIn( self):
        """Adding the current time."""
        t = time.time()
        self.times.append( t)
        return t
    
    def Times( self):
        """Return the time list."""
        return self.times
        

class FrequencyCounter( StopWatch):
    """Frequencies are counted in 1/min."""
    tolerance = 0.2             # max deviation of time differences
    def __init__( self):
        """so what?"""
        StopWatch.__init__( self)
        self.frequencies = []
        self.td_min = 0             # smallest time diff
        self.td_max = 0             # biggest time diff
        
    def TriggerCounter( self):
        """Starting the stopWatch for the first time."""
        self.ClockIn()
        
    def Count( self):
        """Clocking in and counting in 1/min."""
        tt = self.ClockIn()        # the latest time
        t  = self.times[-2:-1][ 0] # the previous time
        diff = tt - t
        #  exception: we need a triggered time list!
        l = len( self.times)
        if l < 2:
            raise IndexError('Counter must first be triggered')        
        if l == 2:
            tol = FrequencyCounter.tolerance
            self.td_min = ( 1 - tol) * diff
            self.td_max = ( 1 + tol) * diff
        if diff > self.td_max or diff < self.td_min:
            return 1             # count not accurate enough
        else:
            self.frequencies.append( 60 / diff )
            return 0

    def Range( self):
        """Return the valid range of frequencies."""
        return 60 / self.td_max, 60 / self.td_min

    def Frequencies( self):
        """Return frequency list."""
        return self.frequencies

    def Times( self):
        """Return time list."""
        return self.times

    def PrintStatus( self):
        """bla"""
        b = len( self.frequencies)
        k = len( self.times)
        print "Keystrokes:", k
        print "Beats counted:", b
        if b > 1:
            bpm =  round( mean( self.frequencies),1)
            std = round( standardDeviation( self.frequencies), 2)
            print "Mean:", str( round( bpm, 1)), "bpm"
            print "Standard deviation", str( std), "bpm"
            bpm =  movingAverage( self.frequencies)
            print "Moving Average:", str( bpm), "bpm"
        
#from time import *
import time
 # time.clock() is the CPU time!
 # time.time() is the Wall (real world) time!
import string

# --- interface stuff ---
import curses

def endCurses():
    """"""
    curses.nocbreak(); stdscr.keypad( 0); curses.echo(); curses.curs_set( 1)
    curses.endwin()                 # restore everything


def tui ( n):                   # text user interface
    """Text User Interphase."""
    stdscr.erase()              # remove vestiges from previous run
    try:
        Fc = FrequencyCounter()
        # addstr uses (y,x) co-ordinates!
        stdscr.addstr( 1, 1, "Press a key to start counting, 'q' to quit.", curses.A_DIM)
        stdscr.addstr( 3, 1, "Run " + str( n) + " waiting.", curses.A_BOLD)
        stdscr.addstr( 5, 1, "Keystrokes: 0", curses.A_DIM)
# --- first count
        c = stdscr.getch()
        if c == ord('q') or c == ord('Q') :
            endCurses()
            return 1
        Fc.TriggerCounter()
        t0 = time.time()          # time.time() is the Wall (real world) time!
        y = 1
        stdscr.addstr( y, 1, "Press 'q' to quit, 'n' to start a new count.    ", curses.A_DIM)
        y = 3
        stdscr.addstr( y, 1, "Run " + str( n) + " active.    ", curses.A_BOLD)
        y = 5
        stdscr.addstr( y, 1, "One keystroke", curses.A_DIM)
# --- second count
        c = stdscr.getch()
        if c == ord('n') or c == ord('N') :
            return 0
        elif c == ord('q') or c == ord('Q') :
            endCurses()
            Fc.PrintStatus()
            return 1
        Fc.Count()
        # documenation 1 DIM
        # Status 3 BOLD
        y = 3
        td = round( time.time() - t0, 1)
        stdscr.addstr( y, 1, "Run " + str( n) + " active for " + str( td) + " s.", curses.A_BOLD)
        # Keystrokes 5 DIM
        y = 5
        l = len( Fc.Times())
        stdscr.addstr( y, 1, "Keystrokes: " +  str( l), curses.A_DIM)
        # Keystrokes discarded 6 BOLD
        # Range 7 DIM
        r1 = int( round( Fc.Range()[ 0]))
        r2 = int( round( Fc.Range()[ 1]))
        y = 7
        stdscr.addstr( y, 1, "Valid beat range: [" + str( r1) + " .. " + str( r2) + "] bpm", curses.A_DIM)
        # Mean 8 BOLD
        y = 8
        bpm = int( round( mean( Fc.Frequencies())))
        stdscr.addstr( y, 1, "Mean:", curses.A_BOLD)
        stdscr.addstr( y, 6, " " + str( bpm) + " bpm ", curses.A_REVERSE)
        # if bpm < 100:   # overwrite possible A_REVERSE from counts faster than 99 bpm
        #             stdscr.addstr( y, 15, " ")
 # --- following counts
        while 1:
            c = stdscr.getch()
            if c == ord('n') or c == ord('N') :
                return 0
            elif c == ord('q') or c == ord('Q') :
                endCurses()
                Fc.PrintStatus()
                return 1
            else:
                if Fc.Count():
                    curses.flash()  # not accurate enough
                # Status BOLD
                y = 3
                td = round( time.time() - t0, 1)
                stdscr.addstr( y, 1, "Run " + str( n) + " active for " + str( td) + " s.", curses.A_BOLD)
                # Keystrokes 5 DIM
                l = len( Fc.Times())
                y = 5
                stdscr.addstr( y, 1, "Keystrokes: " +  str( l), curses.A_DIM)
                # discarded strokes 6 BOLD
                y = 6
                b = len( Fc.Times()) - len( Fc.Frequencies()) - 1
                stdscr.addstr( y, 1, "Skipped keystrokes: " +  str( b), curses.A_BOLD)
                # Range 7 DIM remains constant
                # Mean 8 BOLD
                y = 8
                bpm = int( round( mean( Fc.Frequencies())))
                stdscr.addstr( y, 1, "Mean:", curses.A_BOLD)
                std = round( standardDeviation( Fc.Frequencies()), 1)
                if len( Fc.Frequencies()) > 4 :
                    fl = len( Fc.Frequencies())
                    acc = round( 1.96 * std / math.sqrt( fl), 2)
                    stdscr.addstr( y, 6, " " + str( bpm) + " +/- " + str( acc) + " bpm ", curses.A_REVERSE)
                else :
                    stdscr.addstr( y, 6, " " + str( bpm) + " bpm ", curses.A_REVERSE)
                # if bpm < 100:   # overwrite possible `A_REVERSE' fraom counts faster than 99 bpm
                #     stdscr.addstr( y, 15, " ", curses.A_BOLD)
                # Moving average
                y = 9
                m_bpm = round( movingAverage( Fc.Frequencies()), 1)
                stdscr.addstr( y, 1, "Moving average: " +  str( m_bpm) + " bpm ", curses.A_DIM)
                # Standard & relative deviation
                dev = round( 100 * std / float( bpm), 1) # relative deviation in percent
                y = 10
                stdscr.addstr( y, 1, "Relative deviation: " +  str( dev) + " %  ", curses.A_BOLD)
                y = 11
                stdscr.addstr( y, 1, "Standard deviation: " +  str( std) + " bpm ", curses.A_DIM)
                # Moving deviations
                m_std = round( standardDeviation( Fc.Frequencies()[-10:]), 1) # last 10 
                m_dev = round( 100 * m_std / m_bpm, 1) # relative moving deviation in percent
                y = 12
                stdscr.addstr( y, 1, "Moving relative deviation: " +  str( m_dev) + " % ", curses.A_BOLD)
                y = 13
                stdscr.addstr( y, 1, "Moving standard deviation: " +  str( m_std) + " bpm ", curses.A_DIM)
                
    except KeyboardInterrupt:
        c = stdscr.getch()      # discarding C-c
        stdscr.addstr( 9, 1, "Do you really want to quit?  Print y", curses.A_BOLD)
        c = stdscr.getch()
        if c == ord('y'):
            endCurses()
            return 1
        else:
            stdscr.addstr( 9, 1, "                                           ")
            return 0
    
# ----------------------------------------------------------------------
# run interphase loop ------------------------------

stdscr = curses.initscr()      # this is a screen exactly the terminal size
curses.noecho()                # do not show the keys
curses.cbreak()                # react to keys without Carriage return
curses.curs_set( 0)            # 0: switch off cursor
stdscr.keypad( 1)              # return nice keyboard shortcuts

try:                      # force restoration of terminal
    i = 1                 # run counter
    while not tui( i):
        i = i + 1
finally:
    # reversing the terminal stuff
    endCurses()

# Local variables:
# coding: iso-8859-1
# end:
#######################################################################
