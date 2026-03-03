# 125-SNAKE-GAME
import turtle
import time
import random

# --- Global Settings ---
DELAY_TIME = 0.1
score = 0
high_score = 0
body_segments = []
bad_fruits = [] # List to hold the "Bad Fruit" objects

# --- Screen Setup ---
window = turtle.Screen()
window.title("Snake Game - Watch out for Bad Fruit!")
window.bgcolor("black")
window.setup(width=600, height=600)
window.tracer(0)

# --- Game Objects ---
head = turtle.Turtle()
head.shape("square")
head.color("green")
head.penup()
head.goto(0, 0)
head.direction = "stop"

food = turtle.Turtle()
food.shape("circle")
food.color("red")
food.penup()
food.goto(0, 100)

# Scoreboard
pen = turtle.Turtle()
pen.color("white")
pen.penup()
pen.hideturtle()
pen.goto(0, 260)

# --- Functions ---

def update_score_display(message=""):
    """Updates the score. Requirement: String manipulation."""
    pen.clear()
    # Using string concatenation and formatting
    status = "Score: {}  High Score: {}".format(score, high_score)
    if message != "":
        status = message + " | " + status # String manipulation
    pen.write(status, align="center", font=("Courier", 18, "bold"))

def spawn_bad_fruit():
    """Creates a new 'Bad Fruit' and adds it to the list."""
    bad_apple = turtle.Turtle()
    bad_apple.speed(0)
    bad_apple.shape("circle")
    bad_apple.color("brown") # Brown represents the 'bad' fruit
    bad_apple.penup()
    x = random.randint(-280, 280)
    y = random.randint(-280, 280)
    bad_apple.goto(x, y)
    bad_fruits.append(bad_apple)

def reset_game():
    global score
    update_score_display("GAME OVER!")
    time.sleep(1)
    head.goto(0, 0)
    head.direction = "stop"
    
    for segment in body_segments:
        segment.goto(1000, 1000)
    body_segments.clear()

    # Clear all bad fruits from the screen and list
    for b_fruit in bad_fruits:
        b_fruit.goto(1000, 1000)
    bad_fruits.clear()

    score = 0
    update_score_display()

# Movement Controls
def go_up():
    if head.direction != "down": head.direction = "up"
def go_down():
    if head.direction != "up": head.direction = "down"
def go_left():
    if head.direction != "right": head.direction = "left"
def go_right():
    if head.direction != "left": head.direction = "right"

def move():
    if head.direction == "up": head.sety(head.ycor() + 20)
    if head.direction == "down": head.sety(head.ycor() - 20)
    if head.direction == "left": head.setx(head.xcor() - 20)
    if head.direction == "right": head.setx(head.xcor() + 20)

# Key Bindings
window.listen()
window.onkeypress(go_up, "w")
window.onkeypress(go_down, "s")
window.onkeypress(go_left, "a")
window.onkeypress(go_right, "d")

update_score_display()

# --- Main Game Loop ---
while True:
    window.update()

    # Wall Collisions
    if abs(head.xcor()) > 290 or abs(head.ycor()) > 290:
        reset_game()

    # Food Collision
    if head.distance(food) < 20:
        food.goto(random.randint(-280, 280), random.randint(-280, 280))
        
        # Add body segment
        new_segment = turtle.Turtle()
        new_segment.shape("square")
        new_segment.color("lime")
        new_segment.penup()
        body_segments.append(new_segment)

        score += 10
        if score > high_score:
            high_score = score
        
        # Every 30 points, spawn a 'Bad Fruit' to increase difficulty
        if score % 30 == 0:
            spawn_bad_fruit()
            update_score_display("WATCH OUT!")
        else:
            update_score_display()

    # Requirement: List Indexing for body movement
    for index in range(len(body_segments) - 1, 0, -1):
        x = body_segments[index-1].xcor()
        y = body_segments[index-1].ycor()
        body_segments[index].goto(x, y)

    if len(body_segments) > 0:
        body_segments[0].goto(head.xcor(), head.ycor())

    move()

    # Check for Bad Fruit Collision (Requirement: Iterating through a list)
    for b_fruit in bad_fruits:
        if head.distance(b_fruit) < 20:
            reset_game()

    # Self-collision
    for segment in body_segments:
        if segment.distance(head) < 20:
            reset_game()

    time.sleep(DELAY_TIME)
