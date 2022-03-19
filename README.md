# pygameCOMPLETE

import pygame
import numpy as np
import time

pygame.init()

#Ancho y alto de la pantalla.
width, height = 1000, 1000

#creación de la pantalla.
screen = pygame.display.set_mode((height, width))

bg = 25, 25, 25
#pintamos el fondo de el color deseado.
screen.fill(bg)

#numero de celdas.
nxC, nyC = 30, 30

#dimensiones de la celda.
dimCW = width / nxC
dimCH = height / nyC

#Estado de las celdas. 1 = viva, 2 = muerta.
gameState = np.zeros((nxC, nyC))

# Automata palo,
gameState[5, 3] = 1
gameState[5, 4] = 1
gameState[5, 5] = 1

#autom2
gameState[21, 21] = 1
gameState[22, 22] = 1
gameState[22, 23] = 1
gameState[21, 23] = 1
gameState[20, 21] = 1

#control de la ejecucion del juego
pauseExect = False

#Bucle de ejecución
while True:
    newGameState = np.copy(gameState)

    screen.fill(bg)

    time.sleep(0.1)

    # Registra eventos de teclado y mouse
    ev = pygame.event.get()

    for event in ev:
        if event.type == pygame.KEYDOWN:
            pauseExect = not pauseExect

        mouseClick = pygame.mouse.get_pressed()
        
        if sum(mouseClick) > 0:
            posX, posY = pygame.mouse.get_pos()
            celX, celY = int(np.floor(posX / dimCW)), int(np.floor(posY / dimCH))
            newGameState[celX, celY] = not mouseClick[2]


    for y in range(0, nxC):
        for x in range (0, nyC):

            if not pauseExect:
                #calcular el numero de celdas cercanas.
                n_neight = gameState[(x - 1) % nxC, (y - 1) % nyC] + \
                           gameState[(x)     % nxC, (y - 1) % nyC] + \
                           gameState[(x + 1) % nxC, (y - 1) % nyC] + \
                           gameState[(x - 1) % nxC, (y)     % nyC] + \
                           gameState[(x + 1) % nxC, (y)     % nyC] + \
                           gameState[(x - 1) % nxC, (y + 1) % nyC] + \
                           gameState[(x)     % nxC, (y + 1) % nyC] + \
                           gameState[(x + 1) % nxC, (y + 1) % nyC]                  


                #regla #1: Una celula muerta con 3 vecinas vivas "revive".
                if gameState[x, y] == 0 and n_neight == 3:
                    newGameState[x, y] = 1

                #regla #2: una celula viva con menos de 2 o mas de 3 vecinas vivas muere.  
                elif gameState[x, y] == 1 and (n_neight < 2 or n_neight > 3):
                    newGameState[x, y] = 0

            #creamos el poligono de cada celdas.
            poly = [((x)      * dimCW, y * dimCH),
                    ((x + 1 ) * dimCW, y * dimCH),
                    ((x + 1 ) * dimCW, (y + 1) * dimCH),
                    ((x)      * dimCW, (y + 1) * dimCH)]

            # dibujamos la celda para cada par de "x"e"y".  
            if newGameState[x, y] == 0:   
                pygame.draw.polygon(screen, (128, 128, 128), poly, 1)
            else:
                pygame.draw.polygon(screen, (255, 255, 255), poly, 0)

    #actualizamos el estado del juego.

    gameState = np.copy(newGameState)

    #actualizamos la pantalla.        
    pygame.display.flip()
