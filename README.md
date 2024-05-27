import pygame
import random

# Configurações da tela
LARGURA = 800
ALTURA = 600
COR_PRETO = (0, 0, 0)
COR_BRANCO = (255, 255, 255)
COR_VERDE = (0, 255, 0)

# Inicialização do Pygame
pygame.init()
tela = pygame.display.set_mode((LARGURA, ALTURA))
pygame.display.set_caption("Engine de Jogos Python")

# Função para gerar terreno aleatório
def gerar_terreno(largura, altura, suavizacao):
    terreno = []
    for i in range(altura):
        linha = []
        for j in range(largura):
            altura_aleatoria = random.randint(0, altura)
            # Suavização do terreno
            if suavizacao > 1:
                altura_media = 0
                for k in range(suavizacao):
                    if j - k >= 0 and j + k < largura:
                        altura_media += terreno[i][j - k]
                    if i - k >= 0 and i + k < altura:
                        altura_media += terreno[i - k][j]
                altura_aleatoria = int(altura_media / (suavizacao * 2))
            linha.append(altura_aleatoria)
        terreno.append(linha)
    return terreno

# Função para projetar sombras
def projetar_sombras(terreno, luz_x, luz_y, raio_luz):
    sombras = []
    for i in range(len(terreno)):
        linha_sombras = []
        for j in range(len(terreno[0])):
            distancia_x = abs(luz_x - j)
            distancia_y = abs(luz_y - i)
            distancia = (distancia_x ** 2 + distancia_y ** 2) ** 0.5
            if distancia <= raio_luz:
                nivel_sombra = int(255 - (distancia / raio_luz) * 255)
            else:
                nivel_sombra = 255
            linha_sombras.append(nivel_sombra)
        sombras.append(linha_sombras)
    return sombras

# Loop principal do jogo
rodando = True
while rodando:
    # Eventos do teclado e mouse
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            rodando = False

    # Gerar terreno
    terreno = gerar_terreno(LARGURA, ALTURA, 2)

    # Projetar sombras
    sombras = projetar_sombras(terreno, 200, 100, 150)

    # Desenhar na tela
    tela.fill(COR_PRETO)
    for i in range(len(terreno)):
        for j in range(len(terreno[0])):
            altura = terreno[i][j]
            cor = COR_VERDE * (altura / ALTURA)
            pygame.draw.rect(tela, cor, (j * 5, i * 5, 5, 5))

    # Desenhar sombras
    for i in range(len(sombras)):
        for j in range(len(sombras[0])):
            nivel_sombra = sombras[i][j]
            cor_sombra = (nivel_sombra, nivel_sombra, nivel_sombra)
            pygame.draw.rect(tela, cor_sombra, (j * 5, i * 5, 5, 5))

    # Atualizar a tela
    pygame.display.flip()

# Finalizar o Pygame
pygame.quit()

