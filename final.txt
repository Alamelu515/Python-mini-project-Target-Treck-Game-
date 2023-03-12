# importing essential modules
import turtle
import random
import winsound

# setting up screen
wn=turtle.Screen()
wn.setup(700,700)
wn.title("Target Treck")
wn.tracer() # avoids redrawing the screen everytime

# initial game state
game_state="start"

# defining all necessary functions in the reverse order for calling purpose
def exit():
    turtle.bye() # closes the turtle window and terminates the program

def over(n):
    turtle.Screen().clear()
    wn.bgpic("over.gif")
    screenstring="SCORE:%d"%n
    turtle.color("white")
    turtle.write(screenstring,False,align="center",font=["Arial",30,"bold"])
    turtle.hideturtle()
    wn.listen()
    wn.onkeypress(game,"C") # before game fn is called here, it would have been run once atleast, so the compiler knows
    wn.onkeypress(exit,"E")

def game():
    
    turtle.Screen().clear()
    wn.bgpic("game.gif")
    
    # drawing border
    mypen=turtle.Turtle() # originally at origin
    mypen.speed(0) # essential, animation speed and not movement speed, the speed with which the movements appear on the screen, 0 is fastest and 10 is slowest
    mypen.pensize(3) # shape not required
    mypen.color("white")
    mypen.hideturtle()
    mypen.penup()
    mypen.setposition(-300,-300)
    mypen.pendown()
    for side in range(4):
        mypen.forward(600)
        mypen.left(90)
    
    # creating player
    player=turtle.Turtle()
    player.speed(0)
    player.shape("triangle")
    player.color("lightgreen")
    player.penup()
    
    # creating goals
    maxgoals=5
    goals=[]
    for count in range(maxgoals):
        goals.append(turtle.Turtle())
        goals[count].speed(0)
        goals[count].shape("circle")
        goals[count].color("red")
        goals[count].penup()
        goals[count].setposition(random.randint(-270,270),random.randint(-270,270))
    
    # setting keyboard bindings for player
    def right():
        player.setheading(0)
    def up():
        player.setheading(90)
    def left():
        player.setheading(180)
    def down():
        player.setheading(270)
    turtle.listen()
    turtle.onkey(right,"Right")
    turtle.onkey(up,"Up")
    turtle.onkey(left,"Left")
    turtle.onkey(down,"Down")
    
    # game loop
    score=0
    while True:
        
        # driving player
        player.forward(10)
        
        # boundary checking for player
        if player.xcor()>300 or player.xcor()<-300 or player.ycor()>300 or player.ycor()<-300:
            winsound.PlaySound("game_over.wav",winsound.SND_ASYNC)
            break
        
        # for each goal
        for count in range(maxgoals):
            
            # driving each goal
            goals[count].forward(5)
            
            # collision checking
            if player.distance(goals[count]) < 20:
                score+=1
                mypen.undo() # to avoid overwriting scores
                mypen.hideturtle()
                mypen.penup()
                mypen.setposition(-300,300)
                screenstring="SCORE:%d"%score
                mypen.write(screenstring,False,align="left",font=["Arial",30,"bold"])
                winsound.PlaySound("collision.wav",winsound.SND_ASYNC)
                goals[count].setposition(random.randint(-270, 270), random.randint(-270, 270))

            # boundary checking for each goal
            if goals[count].xcor() > 270:
                winsound.PlaySound("boundary.wav",winsound.SND_ASYNC)
                goals[count].setx(270)
                goals[count].setheading(random.randint(180, 360))
            elif goals[count].xcor() < -270:
                winsound.PlaySound("boundary.wav",winsound.SND_ASYNC)
                goals[count].setx(-270)
                goals[count].setheading(random.randint(0, 180))
            elif goals[count].ycor() > 270:
                winsound.PlaySound("boundary.wav",winsound.SND_ASYNC)
                goals[count].sety(270)
                goals[count].setheading(random.randint(270, 450))
            elif goals[count].ycor() < -270:
                winsound.PlaySound("boundary.wav",winsound.SND_ASYNC)
                goals[count].sety(-270)
                goals[count].setheading(random.randint(90, 270))
            
    # over fn is called when the loop is terminated
    over(score)

def start():
    wn.bgpic("start.gif")
    wn.listen()
    wn.onkeypress(game,"S")

# calling start fn to start the game
start()

# essential, to delay and hold the screen until the user wants to close
delay=input("Press enter to finish")