import pygame
import random
import time

# Inicialización de Pygame
pygame.init()

# Dimensiones de la ventana
WIDTH, HEIGHT = 800, 600
window = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Gato Cazador")

# Colores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
BLUE = (0, 0, 255)

# Carga de imágenes
gato_img = pygame.image.load("gato.png")
gato_img = pygame.transform.scale(gato_img, (150, 150))

raton_img = pygame.image.load("raton.png")
raton_img = pygame.transform.scale(raton_img, (100, 100))

portada_img = pygame.image.load("portada.png")  # Imagen de Tom y Jerry
portada_img = pygame.transform.scale(portada_img, (WIDTH, HEIGHT))

fondo_img = pygame.image.load("fondo.png")  # Imagen de fondo para el juego
fondo_img = pygame.transform.scale(fondo_img, (WIDTH, HEIGHT))

# Cargar sonidos
capture_sound = pygame.mixer.Sound("capture.wav")
pygame.mixer.music.load("background.mp3")

# Posiciones iniciales
gato_pos = [WIDTH // 2, HEIGHT // 2]
raton_pos = [random.randint(0, WIDTH-100), random.randint(0, HEIGHT-100)]

# Variables del juego
score = 0
start_time = time.time()
timer = 60

# Reloj
clock = pygame.time.Clock()

# Fuente
font = pygame.font.Font(None, 36)

# Función para mostrar texto en pantalla
def draw_text(text, font, color, surface, x, y):
    textobj = font.render(text, True, color)
    textrect = textobj.get_rect()
    textrect.topleft = (x, y)
    surface.blit(textobj, textrect)

# Función para manejar la pantalla de inicio
def show_start_screen():
    start_screen = True
    while start_screen:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                mouse_pos = event.pos
                if play_button.collidepoint(mouse_pos):
                    start_screen = False

        window.fill(WHITE)
        window.blit(portada_img, (0, 0))
        pygame.draw.rect(window, BLUE, play_button)
        draw_text("JUGAR", font, WHITE, window, play_button.x + 20, play_button.y + 10)
        pygame.draw.rect(window, BLACK, play_button, 2)

        pygame.display.flip()
        clock.tick(60)

# Función para manejar la pantalla del juego
def show_game_screen():
    global score, start_time, timer, gato_pos, raton_pos

    # Reproducir música de fondo
    pygame.mixer.music.play(-1)

    # Variables del juego
    score = 0
    start_time = time.time()
    timer = 60
    gato_pos = [WIDTH // 2, HEIGHT // 2]
    raton_pos = [random.randint(0, WIDTH-100), random.randint(0, HEIGHT-100)]

    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                mouse_pos = event.pos
                if exit_button.collidepoint(mouse_pos):
                    running = False

        # Movimiento del gato
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            gato_pos[0] -= 5
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            gato_pos[0] += 5
        if keys[pygame.K_UP] or keys[pygame.K_w]:
            gato_pos[1] -= 5
        if keys[pygame.K_DOWN] or keys[pygame.K_s]:
            gato_pos[1] += 5

        # Detección de colisiones
        gato_rect = pygame.Rect(gato_pos[0], gato_pos[1], 150, 150)
        raton_rect = pygame.Rect(raton_pos[0], raton_pos[1], 100, 100)
        
        if gato_rect.colliderect(raton_rect):
            score += 1
            capture_sound.play()
            raton_pos = [random.randint(0, WIDTH-100), random.randint(0, HEIGHT-100)]

        # Actualización del temporizador
        current_time = time.time()
        timer = 30 - int(current_time - start_time)
        if timer <= 0:
            running = False

        # Dibujado
        window.blit(fondo_img, (0, 0))
        window.blit(gato_img, (gato_pos[0], gato_pos[1]))
        window.blit(raton_img, (raton_pos[0], raton_pos[1]))
        draw_text(f"Puntuacion: {score}", font, BLUE, window, 10, 10)
        draw_text(f"Tiempo: {timer}", font, BLUE, window, 10, 50)
        
        pygame.draw.rect(window, BLUE, exit_button)
        draw_text("SALIR", font, WHITE, window, exit_button.x + 10, exit_button.y + 10)
        pygame.draw.rect(window, BLACK, exit_button, 2)

        pygame.display.flip()
        clock.tick(60)

# Función para manejar la pantalla de fin de juego
def show_end_screen():
    end_screen = True
    while end_screen:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                mouse_pos = event.pos
                if restart_button.collidepoint(mouse_pos):
                    end_screen = False
                elif exit_button.collidepoint(mouse_pos):
                    pygame.quit()
                    quit()

        window.fill(WHITE)
        draw_text(f"FIN DEL JUEGO! SU PUNTUACION ES: {score}", font, BLACK, window, WIDTH // 2 - 150, HEIGHT // 2 - 50)
        pygame.draw.rect(window, BLUE, restart_button)
        draw_text("REINICIAR", font, WHITE, window, restart_button.x + 5, restart_button.y + 5)
        pygame.draw.rect(window, BLACK, restart_button, 2)
        
        pygame.draw.rect(window, BLUE, exit_button)
        draw_text("SALIR", font, WHITE, window, exit_button.x + 10, exit_button.y + 10)
        pygame.draw.rect(window, BLACK, exit_button, 2)

        pygame.display.flip()
        clock.tick(60)

# Botón "Jugar"
play_button = pygame.Rect(WIDTH // 2 - 80, HEIGHT // 2 + 150, 130, 50)

# Botón "Salir"
exit_button = pygame.Rect(WIDTH - 110, 10, 100, 50)

# Botón "Reiniciar"
restart_button = pygame.Rect(WIDTH // 2 - 80, HEIGHT // 2 + 50, 150, 50)

# Bucle principal del juego
while True:
    # Mostrar la pantalla de inicio
    show_start_screen()
    
    # Mostrar la pantalla del juego
    show_game_screen()
    
    # Mostrar la pantalla de fin de juego
    show_end_screen()
