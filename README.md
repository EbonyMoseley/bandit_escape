# bandit_escape
This program simulates the well-known "Frogger" game.

Running this code requires importing the Python graphics and random modules.

from graphics import *
import random

#Car class used to make,draw,move,wrap,and
#get locations of each car
class Car:
    #takes in x & y locations and random color choice
    def __init__(self,x,y,color):
        #makes car
        self.body = Rectangle(Point(x-50,y-15),Point(x+50,y+25))
        self.body.setFill(color)
        self.body.setOutline(color)
        self.top = Oval(Point(x-35,y-40),Point(x+35,y+20))
        self.top.setFill(color)
        self.top.setOutline(color)
        self.wheel1 = Circle(Point(x-30,y+25),10)
        self.wheel1.setFill("gray")
        self.wheel1.setWidth(8)
        self.wheel1.setOutline("black")
        self.wheel2 = Circle(Point(x+30,y+25),10)
        self.wheel2.setFill("gray")
        self.wheel2.setWidth(8)
        self.wheel2.setOutline("black")

        #list for parts of car
        self.part_lst = [self.body, self.top, self.wheel1, self.wheel2]

    
    def draw_car(self,window):
        """This helper function is used to draw each car
            by drawing each part of the car using the list of parts."""
        for part in self.part_lst:
            part.draw(window)

    def move_car(self,dx,dy,width):
        """This helper function is used to move each car by
           by moving each part of the car using the lists of parts.
           It also makes the cars appear as if they are wrapping around the
           graphics window."""
        for part in self.part_lst:
            part.move(dx,dy)
            
            x_center = self.body.getCenter().getX()
            #for cars moving right,
            if x_center > width:
                #"wraps" car around window
                for part in self.part_lst:
                    part.move(-width, 0)
            #for cars moving left
            elif x_center < 0:
                #"wraps" car around window
                for part in self.part_lst:
                    part.move(width,0)
                    
    """The methods return the x and y location of each car."""
    def get_car_x(self):
        car_x = self.body.getCenter().getX()
        return car_x
    def get_car_y(self):
        car_y = self.body.getCenter().getY()
        return car_y


#used to display user's scores for lives and crossings
class Banner:

    def __init__(self,count1,count2,width,height,window):

        #used to display number of user's lives
        life_box = Rectangle(Point(2,2),Point(170,60))
        life_box.setFill("red")
        life_box.setOutline("black")
        life_box.setWidth(5)
        life_box.draw(window)
        #takes in count1 from main 
        lives = "LIVES:", str(count1)
        life_text = Text(Point(85,30),lives)
        life_text.setFill("black")
        life_text.setSize(30)
        life_text.setStyle("bold")
        life_text.draw(window)

        #used to display number of user's successful crossings
        cross_box = Rectangle(Point(width-220,2),Point(width-2,60))
        cross_box.setFill("green")
        cross_box.setWidth(5)
        cross_box.draw(window)
        #takes in count2 from main
        crosses = "CROSSES:", str(count2)
        cross_text = Text(Point(width-110,30),crosses)
        cross_text.setFill("black")
        cross_text.setSize(30)
        cross_text.setStyle("bold")
        cross_text.draw(window)

        #once user runs out of lives,
        if count1 == 0:
            #displays "GAME OVER" on window
            new_text = Text(Point(width/2,height/2),"GAME OVER")
            new_text.setFill("red")
            new_text.setSize(35)
            new_text.setStyle("bold")
            new_text.draw(window)
            
        

def make_lines(width,height,window):
    """This helper function is used to make the top and bottom
       boundary lines used to determine where the user should click."""
    #user must click over top line
    top_line = Line(Point(0,height-365),Point(width,height-365))
    top_line.setFill("yellow")
    top_line.setWidth(6)
    top_line.draw(window)
    #or under bottom line
    btm_line = Line(Point(0,height-115),Point(width,height-115))
    btm_line.setFill("yellow")
    btm_line.setWidth(6)
    btm_line.draw(window)

    

def main():
     #dimensions of background image
     width = 852
     height = 478

     win = GraphWin("Runaway Bandit",width,height,autoflush = False)
     
     #sets background to road image
     image_name = "RoadImage.gif"
     road = Image(Point(width/2,height/2),image_name)
     road.draw(win)

     #calls helper function to make boundary lines
     make_lines(width,height,win)

     #list of possible colors for cars
     color_lst = ["light blue","gold","blue","yellow","green","orange","firebrick","pink","red","purple","gray","white","black"]

     #used for relative positions of cars
     x = 100
     y = 300

     #intializes lists used for cars moving right and left
     move_r = []
     move_l = []

     #calls Car class to define each car
     #each car starts(is drawn) in a different location
     car1 = Car(x,y,random.choice(color_lst))
     car2 = Car(x+284,y,random.choice(color_lst))
     car3 = Car(x+586,y,random.choice(color_lst))
     car4 = Car(x+100,y-125,random.choice(color_lst))
     car5 = Car(x+384,y-125,random.choice(color_lst))
     car6 = Car(x+668,y-125,random.choice(color_lst))    

     #list of all cars
     car_lst = [car1,car2,car3,car4,car5,car6]

     #adds first three cars to moving right list
     #calls draw_car method from Car class to draw cars
     for car in car_lst[:3]:
         move_r.append(car)
         car.draw_car(win)
     #adds last three cars to moving left list
     #calls draw_car method to display cars
     for car in car_lst[3:]:
         move_l.append(car)
         car.draw_car(win)
         
     #bandit "home"/starting position
     bandit_x = width/2
     bandit_y = height - 50
     #draws bandit using bandit image
     bandit = Image(Point(bandit_x,bandit_y),"BanditImage.gif")
     bandit.draw(win)

     #initializes score for number of lives left
     count1 = 5
     #for number of times bandit made it across without being hit
     count2 = 0

    
     keep_move = True
     while keep_move:
         #calls Banner class with actual parameters
         Banner(count1,count2,width,height,win)

         #for each car in moving right list,
         for car in move_r:
             #defines car location using helper functions
             car_x,car_y= car.get_car_x(),car.get_car_y()
             #moves car by calling helper function with actual parameters
             car.move_car(3,0,width)
             #if car touches bandit
             if car_x - 85 <= bandit.getAnchor().getX() <= car_x + 85 and car_y + 20 >= bandit.getAnchor().getY() >= car_y - 20:
                #user loses one life
                count1 -= 1
                #moves bandit back to starting location
                bandit.move(0,130)

         #for each car in moving left list,              
         for car in move_l:
             #defines car location using helper functions
             car_x,car_y = car.get_car_x(),car.get_car_y()
             #moves car by calling helper function with actual parameters
             car.move_car(-3,0,width)
             #if car touches bandit
             if car_x - 85 <= bandit.getAnchor().getX() <= car_x + 85 and car_y + 20 >= bandit.getAnchor().getY() >= car_y - 20:
                 #user loses one life
                 count1 -= 1
                 #moves bandit back to starting location
                 bandit.move(0,390)
                 
        
         user_click = win.checkMouse()
         #when the user clicks,
         if user_click != None:
             #if user clicks above top boundary line,
             if user_click.getY() < height-365:
                #move bandit up one increment
                bandit.move(0,-130)
             #is user clicks below bottom boundary line,
             elif user_click.getY() > height-115:
                #move bandit down one increment
                bandit.move(0,130)
         #if bandit passes top boundary line,
         elif bandit.getAnchor().getY() < height-365:
             #add 1 to number of times bandit crossed street
             count2 += 1
             #places bandit back at starting position
             bandit.move(0,390)
         #if bandit is below window,
         elif bandit.getAnchor().getY() > height:
             #places bandit back at starting position
             bandit.move(0,-130)
         #once user runs of out lives,
         elif count1 == 0:
             #calls Banner class to end game
             Banner(count1,count2,width,height,win)
             #ends game by stopping movement
             keep_move = False



main()
