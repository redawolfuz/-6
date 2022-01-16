import pygame as p
import random
from pygame.locals import *
import copy
CELL_COLOR = (60, 190, 1)
BACKGROUND = (255, 248, 220)
TEXT_COLOR = (138, 43, 226)
p.display.set_caption('Game of Life')
root = p.display.set_mode((500, 500))
n = root.get_height() // 20
m = root.get_width() // 20
p.font.init()
myfont = p.font.SysFont('Times New Roman', 25, bold=True)
class Cell:
    cells = set()
    def __init__(self, x, y, alive):
        self.x = x
        self.y = y
        self.alive = alive
        Cell.cells.add(self)
        return
    def kill(self):
        self.alive = False
        return
    def resurrect(self):
        self.alive = True
        return
    def alive_neighbours(self):
        count = 0
        for z in Cell.cells:
            if z.alive:
                if abs(self.x - z.x) < 2 and abs(self.y - z.y) < 2:
                    count += 1
        if self.alive:
            count -= 1
        return count
class Game:
    epoch = 0
    def __init__(self, n, m):
        self.n = n
        self.m = m
        return
    def setup(self):
        for i in range(n):
            for j in range(m):
                Cell(i, j, random.choice([True, False]))
        return
    def stage(self):
        for i in p.event.get():
            if i.type == QUIT:
                quit()
        for z in Cell.cells:
            p.draw.rect(root, (CELL_COLOR if z.alive else BACKGROUND), [z.y * 20, z.x * 20, 20, 20])
        self.epoch += 1
        textsurface = myfont.render('Epoch: ' + str(self.epoch), False, TEXT_COLOR)
        root.blit(textsurface, (10, 0))
        p.display.update()
        new_cells = set()
        for cell in Cell.cells:
            new_cell = copy.deepcopy(cell)
            if cell.alive_neighbours() not in (2, 3):
                new_cell.kill()
            if cell.alive_neighbours() == 3:
                new_cell.resurrect()
            new_cells.add(new_cell)
        Cell.cells = copy.deepcopy(new_cells)
        return

    def play(self):
        self.setup()
        while 1:
            self.stage()
        return
a = Game(n, m)
a.play()
