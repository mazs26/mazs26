import pygame
import sys

# Inicializar Pygame
pygame.init()

# Constantes
ANCHO, ALTO = 800, 600
VERDE = (0, 128, 0)
BLANCO = (255, 255, 255)

# Crear la ventana
ventana = pygame.display.set_mode((ANCHO, ALTO))

# Crear la cancha
cancha = pygame.Rect(0, 0, ANCHO, ALTO)

# Crear las líneas de la cancha
linea_central = pygame.Rect(ANCHO / 2, 0, 2, ALTO)
linea_lateral_izquierda = pygame.Rect(0, 0, 2, ALTO)
linea_lateral_derecha = pygame.Rect(ANCHO - 2, 0, 2, ALTO)
linea_superior = pygame.Rect(0, 0, ANCHO, 2)
linea_inferior = pygame.Rect(0, ALTO - 2, ANCHO, 2)

# Crear las porterías
porteria_izquierda = pygame.Rect(10, ALTO / 2 - 50, 10, 100)
porteria_derecha = pygame.Rect(ANCHO - 20, ALTO / 2 - 50, 10, 100)

# Crear el jugador
jugador_images_derecha = [
    pygame.image.load('hombre_derecha1.png'),
    pygame.image.load('hombre_derecha2.png'),
    pygame.image.load('hombre_derecha3.png'),
    pygame.image.load('hombre_derecha4.png'),
    pygame.image.load('hombre_derecha5.png'),
    pygame.image.load('hombre_derecha6.png'),
    pygame.image.load('hombre_derecha7.png')
]

jugador_images_izquierda = [
    pygame.image.load('hombre_izquierda1.png'),
    pygame.image.load('hombre_izquierda2.png'),
    pygame.image.load('hombre_izquierda3.png'),
    pygame.image.load('hombre_izquierda4.png'),
    pygame.image.load('hombre_izquierda5.png'),
    pygame.image.load('hombre_izquierda6.png'),
    pygame.image.load('hombre_izquierda7.png')
]

jugador_rect = jugador_images_derecha[0].get_rect()
jugador_rect.x = ANCHO / 2
jugador_rect.y = ALTO / 2

# Variable para controlar la animación
animacion_correr = 0
tiempo_animacion = 0

# Velocidad del jugador
velocidad_jugador = 3

# Centro del campo y radio del círculo
centro_campo = (ANCHO // 2, ALTO // 2)
radio_centro_campo = 20

# Variables adicionales
balon_rect = pygame.Rect(0, 0, 10, 10)  # Rectángulo del balón
balon_velocidad = 3  # Velocidad del balón
balon_en_juego = True  # Bandera para indicar si el balón está en juego

# Inicializar la posición del balón en los pies del jugador
balon_rect.x = jugador_rect.centerx  # Centrar el balón en el eje x
balon_rect.y = jugador_rect.bottom  # Posicionar el balón en los pies del jugador

# Bucle principal del juego
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    # Manejar eventos de teclado
    keys = pygame.key.get_pressed()
    if keys[pygame.K_UP]:
        jugador_rect.y -= velocidad_jugador
    if keys[pygame.K_DOWN]:
        jugador_rect.y += velocidad_jugador
    if keys[pygame.K_LEFT]:
        jugador_rect.x -= velocidad_jugador
    if keys[pygame.K_RIGHT]:
        jugador_rect.x += velocidad_jugador

    if keys[pygame.K_SPACE] and not balon_en_juego:
        # Iniciar balón en los pies del jugador
        balon_rect.x = jugador_rect.x + jugador_rect.width / 2
        balon_rect.y = jugador_rect.y + jugador_rect.height
        balon_en_juego = True

    #Actualizar la posicion del balon
    if balon_en_juego:
        balon_rect.y += balon_velocidad
        if balon_rect.y > ALTO:
            balon_en_juego= False

    # Animación de correr
    if keys[pygame.K_UP] or keys[pygame.K_DOWN] or keys[pygame.K_LEFT] or keys[pygame.K_RIGHT]:
        tiempo_animacion += 1
        if tiempo_animacion >= 5:  # Cambiar de imagen cada 5 frames
            animacion_correr = (animacion_correr + 1) % len(jugador_images_derecha)
            tiempo_animacion = 0

    if keys[pygame.K_LEFT]:
        jugador_rect.x -= velocidad_jugador
        jugador_image = jugador_images_izquierda[animacion_correr]
    elif keys[pygame.K_RIGHT]:
        jugador_rect.x += velocidad_jugador
        jugador_image = jugador_images_derecha[animacion_correr]
    else:
        if jugador_rect.x > ANCHO / 2:  # Si el jugador está en el lado derecho
            jugador_image = jugador_images_derecha[0]  # Mostrar imagen hacia la derecha
        else:
            jugador_image = jugador_images_izquierda[0]  # Mostrar imagen hacia la izquierda

    # Dibujar la cancha
    ventana.fill(VERDE)

    # Dibujar las líneas de la cancha
    pygame.draw.rect(ventana, BLANCO, linea_central)
    pygame.draw.rect(ventana, BLANCO, linea_lateral_izquierda)
    pygame.draw.rect(ventana, BLANCO, linea_lateral_derecha)
    pygame.draw.rect(ventana, BLANCO, linea_superior)
    pygame.draw.rect(ventana, BLANCO, linea_inferior)

    # Dibujar las porterías
    pygame.draw.rect(ventana, BLANCO, porteria_izquierda)
    pygame.draw.rect(ventana, BLANCO, porteria_derecha)

    # Dibujar el círculo del medio del campo
    pygame.draw.circle(ventana, BLANCO, centro_campo, radio_centro_campo, 4)

    # Dibujar el jugador
    ventana.blit(jugador_image, jugador_rect)

    #Dibujar el balon
    if balon_en_juego:
        pygame.draw.rect(ventana, BLANCO, balon_rect)

    # Actualizar la ventana
    pygame.display.update()

    # Limitar la tasa de cuadros por segundo
    pygame.time.Clock().tick(60)
