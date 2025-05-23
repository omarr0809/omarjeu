# omarjeu
import pygame  
import sys     
import random  

pygame.init()  
fenetre = pygame.display.set_mode((600, 600))  
pygame.display.set_caption("Mini-jeux Pygame")  
police = pygame.font.SysFont("arial", 32)  

MENU = "menu"
JEU1 = "jeu2048"
JEU2 = "tictactoe"
etat = MENU  

def afficher_menu():
    fenetre.fill((0, 0, 0)) 
    texte1 = police.render("1. 2048", True, (255, 255, 255))  
    texte2 = police.render("2. Tic Tac Toe", True, (255, 255, 255))  
    fenetre.blit(texte1, (100, 200))  
    fenetre.blit(texte2, (100, 250))  
    pygame.display.flip() 


def jeu_2048():
    taille = 4 
    grille = []
    for i in range(taille):  
        ligne = []
        for j in range(taille):
            ligne.append(0)  
        grille.append(ligne)

    # Ajoute un "2" dans une case vide
    def ajouter_nouveau():
        cases_vides = []
        for i in range(taille):
            for j in range(taille):
                if grille[i][j] == 0:
                    cases_vides.append((i, j))
        if len(cases_vides) > 0:
            i, j = random.choice(cases_vides)
            grille[i][j] = 2

    # Fusionne les cases identiques d'une ligne
    def fusionner(ligne):
        nouvelle = []
        for v in ligne:
            if v != 0:
                nouvelle.append(v)
        i = 0
        while i < len(nouvelle) - 1:
            if nouvelle[i] == nouvelle[i + 1]:
                nouvelle[i] *= 2  
                nouvelle.pop(i + 1)
                nouvelle.append(0)
            i += 1
        while len(nouvelle) < taille:
            nouvelle.append(0)
        return nouvelle

    # Fonction pour déplacer les cases dans une direction
    def deplacer(direction):
        if direction == "gauche":
            for i in range(taille):
                nouvelle = fusionner(grille[i])
                for j in range(taille):
                    grille[i][j] = nouvelle[j]
        elif direction == "droite":
            for i in range(taille):
                ligne_inverse = []
                for j in range(taille - 1, -1, -1):
                    ligne_inverse.append(grille[i][j])
                fusion = fusionner(ligne_inverse)
                for j in range(taille):
                    grille[i][taille - 1 - j] = fusion[j]
        elif direction == "haut":
            for j in range(taille):
                colonne = []
                for i in range(taille):
                    colonne.append(grille[i][j])
                fusion = fusionner(colonne)
                for i in range(taille):
                    grille[i][j] = fusion[i]
        elif direction == "bas":
            for j in range(taille):
                colonne_inverse = []
                for i in range(taille - 1, -1, -1):
                    colonne_inverse.append(grille[i][j])
                fusion = fusionner(colonne_inverse)
                for i in range(taille):
                    grille[taille - 1 - i][j] = fusion[i]

    ajouter_nouveau()
    ajouter_nouveau()

    # Boucle du jeu
    while True:
        fenetre.fill((0, 0, 0))  

        # Affichage de la grille
        for i in range(taille):
            for j in range(taille):
                val = grille[i][j]
                couleur = (200, 200, 200) if val == 0 else (255, 255 - min(val, 255), 0)
                pygame.draw.rect(fenetre, couleur, (j * 150 + 25, i * 150 + 25, 100, 100))
                if val != 0:
                    texte = police.render(str(val), True, (0, 0, 0))
                    fenetre.blit(texte, (j * 150 + 50, i * 150 + 50))
        pygame.display.flip()

        # Gestion des touches
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                return "quitter"
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    deplacer("gauche")
                    ajouter_nouveau()
                elif event.key == pygame.K_RIGHT:
                    deplacer("droite")
                    ajouter_nouveau()
                elif event.key == pygame.K_UP:
                    deplacer("haut")
                    ajouter_nouveau()
                elif event.key == pygame.K_DOWN:
                    deplacer("bas")
                    ajouter_nouveau()


def jeu_tictactoe():
    grille = []
    for i in range(3):
        ligne = []
        for j in range(3):
            ligne.append(None)  # None = case vide
        grille.append(ligne)

    joueur = "X"  # Le joueur commence par X

    # Dessine la grille et les symboles
    def dessiner_grille():
        fenetre.fill((0, 0, 0))
        # Lignes de la grille
        pygame.draw.line(fenetre, (255, 255, 255), (0, 200), (600, 200), 3)
        pygame.draw.line(fenetre, (255, 255, 255), (0, 400), (600, 400), 3)
        pygame.draw.line(fenetre, (255, 255, 255), (200, 0), (200, 600), 3)
        pygame.draw.line(fenetre, (255, 255, 255), (400, 0), (400, 600), 3)
        # Symboles
        for ligne in range(3):
            for colonne in range(3):
                symbole = grille[ligne][colonne]
                centre_x = colonne * 200 + 100
                centre_y = ligne * 200 + 100
                if symbole == "O":
                    pygame.draw.circle(fenetre, (0, 255, 255), (centre_x, centre_y), 60, 5)
                elif symbole == "X":
                    pygame.draw.line(fenetre, (255, 0, 0), (centre_x - 50, centre_y - 50), (centre_x + 50, centre_y + 50), 5)
                    pygame.draw.line(fenetre, (255, 0, 0), (centre_x + 50, centre_y - 50), (centre_x - 50, centre_y + 50), 5)
        pygame.display.flip()

    # Vérifie si un joueur a gagné
    def verifier_victoire():
        for i in range(3):
            if grille[i][0] == grille[i][1] == grille[i][2] and grille[i][0] is not None:
                return True
        for j in range(3):
            if grille[0][j] == grille[1][j] == grille[2][j] and grille[0][j] is not None:
                return True
        if grille[0][0] == grille[1][1] == grille[2][2] and grille[0][0] is not None:
            return True
        if grille[0][2] == grille[1][1] == grille[2][0] and grille[0][2] is not None:
            return True
        return False

    # Boucle du jeu
    while True:
        dessiner_grille()
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                return "quitter"
            if event.type == pygame.MOUSEBUTTONDOWN:
                x, y = pygame.mouse.get_pos()
                colonne = x // 200
                ligne = y // 200
                if grille[ligne][colonne] is None:
                    grille[ligne][colonne] = joueur
                    if verifier_victoire():
                        dessiner_grille()
                        message = police.render(joueur + " gagne !", True, (0, 255, 0))
                        fenetre.blit(message, (200, 550))
                        pygame.display.flip()
                        pygame.time.wait(2000)
                        return
                    if joueur == "X":
                        joueur = "O"
                    else:
                        joueur = "X"

#Boucle principale
clock = pygame.time.Clock()
en_cours = True
while en_cours:
    if etat == MENU:
        afficher_menu()
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                en_cours = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_1:
                    etat = JEU1
                elif event.key == pygame.K_2:
                    etat = JEU2

    elif etat == JEU1:
        if jeu_2048() == "quitter":
            en_cours = False
        etat = MENU
    elif etat == JEU2:
        if jeu_tictactoe() == "quitter":
            en_cours = False
        etat = MENU
    clock.tick(30)  

pygame.quit()  
sys.exit()     
