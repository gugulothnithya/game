# game
game for kids
import pygame, random

pygame.init()
SCREEN_WIDTH, SCREEN_HEIGHT = 400,600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 36)

# Bird
bird = pygame.Rect(50, SCREEN_HEIGHT//2, 40,30)
velocity = 0; gravity = 0.5; flap = -7
pipes = []; pipe_gap=200; pipe_w=70; pipe_vel=-2
score = 0

def draw():
    screen.fill((255,255,255))
    pygame.draw.rect(screen, (0,0,0), bird)
    for p in pipes:
        pygame.draw.rect(screen, (0,255,0), p['top'])
        pygame.draw.rect(screen, (0,255,0), p['bottom'])
    score_s = font.render(f"Score: {score}", True, (0,0,0))
    screen.blit(score_s, (10,10))

def check_collision():
    if bird.top < 0 or bird.bottom > SCREEN_HEIGHT:
        return True
    for p in pipes:
        if bird.colliderect(p['top']) or bird.colliderect(p['bottom']):
            return True
    return False

# Game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT: running=False
        if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            velocity = flap

    velocity += gravity
    bird.y += velocity

    # Pipe logic
    if not pipes or pipes[-1]['top'].x < SCREEN_WIDTH - 300:
        h = random.randint(100, SCREEN_HEIGHT - pipe_gap - 100)
        top = pygame.Rect(SCREEN_WIDTH, 0, pipe_w, h)
        bottom = pygame.Rect(SCREEN_WIDTH, h + pipe_gap, pipe_w, SCREEN_HEIGHT - h - pipe_gap)
        pipes.append({'top': top, 'bottom': bottom})

    for p in list(pipes):
        p['top'].x += pipe_vel
        p['bottom'].x += pipe_vel
        if p['top'].right < 0:
            pipes.remove(p)
            score += 1

    if check_collision():
        running = False

    draw()
    pygame.display.flip()
    clock.tick(30)

pygame.quit()
