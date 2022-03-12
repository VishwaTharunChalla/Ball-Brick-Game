# The Brick Breaker Game

# import liabries
import turtle
import random

screen = turtle.Screen()
screen.tracer(0)
screen.bgcolor("grey")

# Set up the boundry turtle so there will be a boundry for the game
boundaryT = turtle.Turtle()
boundaryT.color("blue")
boundaryT.speed(100)
boundaryT.penup()
boundaryT.goto(-250,250)
boundaryT.pendown()
boundaryT.begin_fill()

for x in range(4):
  boundaryT.forward(500)
  boundaryT.right(90)
  boundaryT.begin_fill()
boundaryT.end_fill()
boundaryT.ht()

screen.update()

# Set up the score turtle to keep track of how many points the player has won
scoreT = turtle.Turtle()
scoreT.color("light steel blue")
scoreT.speed(100)
scoreT.penup()
scoreT.goto(-250,260)
score = 0
scoreT.write("Score: " + str(score), font=("Fixedsys",12,"normal"))
scoreT.ht()

# set up the player turtle(paddle)
player = turtle.Turtle()
player.penup()
player.goto(-100,-240)
player.color("pink")

screen.register_shape("paddle",((0,0),(20,0),(20,100),(0,100)))
player.shape("paddle")

ball = turtle.Turtle()
ball.shape("circle")
ball.color("orange")
ball.penup()
ball.goto(-50,-210)
ball.setheading(random.randint(1,179))

screen.update()
# Create an empty list
# add the bricks turtle to the list and set them up
# when setting up create 3 rows of the brick turtles
screen.register_shape("brick",((0,0),(10,0),(10,50),(0,50)))

colors = ["blue", "red", "green","yellow"]
def makeRow(x,y,colors):
  index = random.randint(0,len(colors)-1)
  row = []
  for i in range(7):
    targetT = turtle.Turtle()
    targetT.speed(100)
    targetT.shape("brick")
    targetT.color(colors[index])
    targetT.penup()
    targetT.goto(x + 68*i,y)
    targetT.pendown()
    row.append(targetT)
    index = random.randint(0,len(colors)- 1)
  return row


screen.update()

# Write the event Listeners (left and right)
def right():
  player.forward(70)

screen.onkey(right, "Right")


def left():
  player.backward(70)

screen.onkey(left, "Left")

screen.listen()

# def checkcollison between the ball and obj

# return true or false
def checkCollisonBrick(obj):
  if abs(ball.xcor() - obj.xcor()) < 50 and obj.ycor() <= ball.ycor() <= obj.ycor() + 10 :
    print("colided with the brick:", obj)
    return True
  return False

def checkCollisonPaddle(obj):
  if abs(ball.xcor() - obj.xcor()) < 100 and (obj.ycor() <= ball.ycor() <= obj.ycor() + 20) :
    return True

  return False

#ball.left(90)
# def the bounce so the ball will bounce of things not just turn 180
def bounce(context):
  #top
  if context == "top" or context == "paddle":
    ball.setheading(360 - ball.heading())
  #top half of screen
  elif 180 > ball.heading() >= 0:
    ball.setheading(180 - ball.heading())
  #bottom half
  elif 180 <= ball.heading() < 360:
    ball.setheading(540 - ball.heading())

screen.update()

def countList():
  count = []
  for i in range(8):
    count.append(0)
  return count

def hitBrick(row, count, goal):
  global score
  for x in range(len(row)):
    #checks to see if this specific brick collided with ball
    if checkCollisonBrick(row[x]):
      #checks if brick should disappear
      if count[x] > goal:
        row[x].ht()
        row[x].penup()
        row[x].goto(-1000,1000)
      else:
        #brick can still be hit more
        count[x] += 1
        bounce("paddle")

      score = updateScore(score)

  return count

def updateScore(score):
  score += 5
  scoreT.clear()
  scoreT.write("Score: " + str(score), font=("Fixedsys",12,"normal"))
  return score

row1 = makeRow(-230,200,colors)
count1 = countList()
row2 = makeRow(-230,170,colors)
count2 = countList()
row3 = makeRow(-230,140,colors)
count3 = countList()
row4 = makeRow(-230,110,colors)
count4 = countList()
row5 = makeRow(-230,80,colors)
count5 = countList()
row6 = makeRow(-230,50,colors)
count6 = countList()
row7 = makeRow(-230,20,colors)
count7 = countList()

gameContinue=True

#while loop
while gameContinue:
  ball.forward(8)

  screen.update()
  # if checkcollison with ball and paddle then the ball sould bounce off
  

  

  count1 = hitBrick(row1, count1, 7)

  count2 = hitBrick(row2, count2, 6)

  count3 = hitBrick(row3, count3, 5)

  count4 = hitBrick(row4, count4, 4)

  count5 = hitBrick(row5, count5, 3)

  count6 = hitBrick(row6, count6, 2)

  count7 = hitBrick(row7, count7, 1)


  #if score = 100 then boundry turtle will write you win on the screen
  #5 points each time ball hits one brick

  if ball.ycor() < -240:
    boundaryT.penup()
    boundaryT.goto(-200,0)
    boundaryT.color("midnight blue")
    boundaryT.write("YOU LOSE! :( ", font=("Fixedsys",48,"normal"))
    gameContinue=False

  if score == 720:
    boundaryT.penup()
    boundaryT.goto(-200,0)
    boundaryT.color("orange")
    boundaryT.write("You Win!!! :)", font=("Fixedsys",48,"normal"))
    gameContinue=False

  # if the ball hits the top, left, and right side it should bounce back

  #right side
  if ball.xcor() > 240:
    bounce("right")

  #left side
  if ball.xcor() < -240:
    bounce("left")

  #top side
  if ball.ycor() > 240:
    bounce("top")

  #hits paddle
  if checkCollisonPaddle(player):
    bounce("paddle")

  #if paddle hits the right edge
  if player.xcor() + 100  > 240:
    player.backward(10)

  #if paddle hits the left edge
  if player.xcor() < -240:
    player.forward(10)

  # if the ball hits the botttom of the screen then while loop should stop

  screen.update()
