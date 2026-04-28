# finalProjectStarWarsPlatformerEscapeRoom
# Preproposal

## What idea(s) do you have for your final project?

A Financial literacy themed 2d escape room using dictionaries and series of loops and algorithms. Uses dictionaries to store rooms and data, gameplay loops, and conditions for decision making. Pygame. player makes financial decisions to escape a series of scenarios like debt, budgeting, and saving.

## If you plan to collaborate with one or two classmates, what are their names?

I dont plan to collaborate.

## Do you have any questions of your own?

N/A

# Status Report

#### Your name

Vithun Sandron Jayaprakash

#### Your section leader's name

Section 1 (Nathan Cherny)

#### Project title

Test Your Skills on Financial Literacy: 2D Escape Room

***

Short answers for the below questions suffice. If you want to alter your plan for your project (and obtain approval for the same), be sure to email your section leader directly!

#### What have you done for your project so far?

Use dictionaries for my escape rooms. Each saved as budgets, debts, and savings, finishing up some loops. Just the code for the blueprint 

#### What have you not done for your project yet?

Rendered images, storing files(have to plan that out). Need to add movement, gravity, doors with questions, item(keys, clues), collision animations, sprites and effects, backgrounds for each room, credit score and emergency fund questions.

#### What problems, if any, have you encountered?

Storing files in idle, and code for the loops.

import pygame 
import random 
import time

pygame.init()

=========================

SCREEN

=========================

WIDTH, HEIGHT = 900, 600 screen = pygame.display.set_mode((WIDTH, HEIGHT)) pygame.display.set_caption("Financial Escape Platformer")

clock = pygame.time.Clock() font = pygame.font.SysFont("arial", 24)

=========================

SIMPLE SOUNDS (no files needed)

=========================

try: pygame.mixer.init() jump_sound = pygame.mixer.Sound(None) except: jump_sound = None

=========================

QUESTIONS

=========================

question_pool = [ ("Income $3000, expenses $1800. Left?", ["1200", "1000", "1500"], "1200"), ("Best long-term choice?", ["Save", "Invest", "Spend"], "Invest"), ("Emergency fund should cover?", ["3-6 months", "1 week", "1 year"], "3-6 months"), ("Paying debt on time improves?", ["Credit Score", "Debt", "Spending"], "Credit Score") ]

=========================

GAME STATE

=========================

player = pygame.Rect(50, 500, 40, 50)

gravity = 0 jump_power = -12 on_ground = False

score = 0 has_key = False show_question = False selected_answer = 0

level = 1 max_levels = 3

start_time = time.time() time_limit = 60

=========================

LEVEL SETUP (simple rooms)

=========================

def make_level(): return { "door": pygame.Rect(800, 500, 60, 60), "key": pygame.Rect(random.randint(200, 700), 500, 30, 30), "platform": pygame.Rect(0, 550, 900, 50) }

current_level = make_level()

current_question = None

=========================

FUNCTIONS

=========================

def get_question(): return random.choice(question_pool)

def check_answer(q, choice): global score

if q[1][choice] == q[2]:
    score += 15
    return True
else:
    score -= 10
    return False
def draw_text(text, x, y): img = font.render(text, True, (255,255,255)) screen.blit(img, (x, y))

=========================

MAIN LOOP

=========================

running = True

while running: clock.tick(60) screen.fill((25, 25, 40))

# =========================
# TIMER
# =========================
if time.time() - start_time > time_limit:
    draw_text("TIME'S UP!", 350, 250)
    pygame.display.flip()
    continue

remaining = int(time_limit - (time.time() - start_time))

# =========================
# EVENTS
# =========================
for event in pygame.event.get():
    if event.type == pygame.QUIT:
        running = False

    # question controls
    if show_question:
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP:
                selected_answer = (selected_answer - 1) % 3
            if event.key == pygame.K_DOWN:
                selected_answer = (selected_answer + 1) % 3
            if event.key == pygame.K_RETURN:

                if check_answer(current_question, selected_answer):
                    level += 1
                    if level > max_levels:
                        running = False
                    else:
                        current_level = make_level()
                        player.x = 50
                        player.y = 500

                show_question = False

# =========================
# KEYS
# =========================
keys = pygame.key.get_pressed()

if not show_question:

    # LEFT / RIGHT movement
    if keys[pygame.K_LEFT]:
        player.x -= 5
    if keys[pygame.K_RIGHT]:
        player.x += 5

    # JUMP (platformer physics)
    if keys[pygame.K_SPACE] and on_ground:
        gravity = jump_power
        on_ground = False

# =========================
# GRAVITY
# =========================
gravity += 0.5
player.y += gravity

# ground collision
if player.colliderect(current_level["platform"]):
    player.bottom = current_level["platform"].top
    gravity = 0
    on_ground = True

# =========================
# KEY SYSTEM
# =========================
if player.colliderect(current_level["key"]):
    has_key = True

# =========================
# DOOR SYSTEM
# =========================
if player.colliderect(current_level["door"]) and has_key and not show_question:
    current_question = get_question()
    show_question = True
    selected_answer = 0

# =========================
# DRAW WORLD
# =========================
pygame.draw.rect(screen, (0, 200, 0), player)          # player
pygame.draw.rect(screen, (200, 0, 0), current_level["door"])  # door
pygame.draw.rect(screen, (255, 215, 0), current_level["key"]) # key
pygame.draw.rect(screen, (70, 70, 90), current_level["platform"]) # ground

# UI
draw_text("Score: " + str(score), 10, 10)
draw_text("Level: " + str(level), 10, 40)
draw_text("Time: " + str(remaining), 10, 70)

# =========================
# QUESTION UI
# =========================
if show_question:
    pygame.draw.rect(screen, (0,0,0), (150,150,600,300))

    draw_text(current_question[0], 170, 180)

    for i in range(3):
        color = (0,255,0) if i == selected_answer else (255,255,255)
        img = font.render(current_question[1][i], True, color)
        screen.blit(img, (200, 250 + i*40))

    draw_text("Use UP/DOWN + ENTER", 200, 380)

pygame.display.flip()
pygame.quit()
