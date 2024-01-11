# Seeeee
from random import randint
class BoardException(Exception):
    pass
class BoardOutException(BoardException):
    def __str__(self):
        return "Игрок 1 пробуй еще раз"
class BoardUsedException(BoardException):
    def __str__(self):
        return "Измени прицел, нельзя бить в одно и тоже место дважды"
class BoardShipException(BoardException):
    pass
class Dot:
    def __init__(self,x,y):
        self.x = x
        self.y = y
    
    def __eq__(self,other):
        return self.x== other.x and self.y==other.y
class Ship:
    def __init__(self, l, t, o):
        self.l = l
        self.t = t
        self.o = o
        self.lives = t
        
    @property
    def ship(self):
        ship_dots = []
        for i in range(self.l):
            cur_x = self.t.x
            cur_y = self.t.y
        
        if self.o == o:
            cur_y += i
        if self.o == 1:
            cur_x += i
        ship_dots.append(Dot(cur_x, cur_y))
        return ship_dots
    def shooten(self,shot):
        return ship_dots
class Board:
    def __init__(self, hid = False, size = 6):
        self.hid = hid
        self.size = size
        
        self.count = 0
        self.field = [["0"] * size for _ in range(size)]
        
        self.busy = []
        self.ships = []
    def add_ship(self,ship):
        for d in ship.dots:
            if self.out(d) or d in self.busy:
                raise BoardWrongShipException()
        for d in ship.dots:
            self.field[d.x][d.y] = " ■ "
            self.busy.append(d)
            
        self.ships.append(ship)
        self.contour(ship)
        
    def contour(self,ship, verb=False):
        near = [
            (-1, -1), (-1, 0), (-1, 1),
            (0, -1), (0, 0), (0, 1),
            (1, -1), (1, 0), (1, 1)
            ]
        for d in ship.dots:
            for dx, dy in near:
                cur = Dot(d.x + dx, d.y + dy)
                if not(self.out(cur)) and cur not in self.busy:
                    if verb:
                        self.field[cur.x][cur.y] = " . "
                    self.busy.append(cur)
    def __str__(self):
        res = ""
        res += " | 1 | 2 | 3 | 4 | 5 | 6 |"
        for i, row in enumerate(self.field):
            res += f"\n{i + 1} | " + " | ".join(row) + " |"
        if self.hid:
            res = res.replace("■", "O")
        return res
    def out(self,d):
        return not((0 <= d.x < self.size) and ( 0 <= d.y <self.size))
    def shot(self, d):
        if self.out(d):
            raise BoardOutException()
        if d in self.busy:
            raise BoardUsedException()
            
        self.busy.append(d)
        for ship in self.ships:
            if d in ship.dots:
                ship.lives -= 1
                self.field[d.x][d.y] = "X"
                if ship.lives == 0:
                    self.count += 1
                    self.contour(ship,verb=True)
                    print("Убит")
                    return False
                else:
                    print("Попал, но пока жив")
                    return True
        self.field[d.x][d.y] = "."
        print("Промахнулся")
        return False
    def begin(self):
        self.busy = []

class Player:
    def __init__(self,myboard, urboard):
        self.myboard= myboard
        self.urboard = urboard
    def ask(self):
        raise NotImplemetedError()
    def move(self):
        while True:
            try:
                target = self.ask()
                repeat = self.urboard.shot(target)
            except BoardException as e:
                print(e)
class AI(Player):
    def ask(self):
        d = Dot(randint(0,5), randint(0,5))
        print(f"Ход: {d.x + 1} {d.y + 1}")
        return d
class Used(Player):
    def ask(self):
        while True:
            cords = input("Твой хож: ").split()
            if len(cords) != 2:
                print("Введите 2 координата")
                continue
            x,y = cords
            if not(x.isdight()) or not (y.isdight()):
                print("Введите числа")
                continue
            x,y = int(x), int(y)
            return Dot(x-1, y-1)
class Game:
    def __init__(self, size=6):
        self.size=size
        pl = self.random_board()
        co = self.random_board()
        co.hid = True
        self.ai = Ai(co, pl)
        self.us = User(pl, co)
    def random_board(self):
        board = None
        while board is None:
            board = self.random_place()
        return board
    def random_place(self):
        lens = [3, 2, 2, 1, 1, 1, 1]
        board = Board(size=self.size)
        attempts = 0
        for l in lens:
            while True:
                attempts += 1
                if attempts > 2000:
                    return None
                ship = Ship(Dot(randint(0, self.size), randint(0,self.size)), 1, randint(0,1))
                try:
                    board.add_ship(ship)
                    break
                except BoardWrongShipException:
                    pass
        board.begin()
        return board
    def greet(self):
        print("----------------------------------")
        print("           Добро пожаловать       ")
        print("               в игру             ")
        print("             морской бой          ")
        print("----------------------------------")
        print(" формат ввода: x y (через пробел) ")
        print("       x - номер строки           ")
        print("       y - номер столбца          ")
    @staticmethod
    def vstack(s1,s2):
        s1 = s1.split("\n")
        s2 = s2.split("\n")
        maxlen = max(map(len,s1))
        result = ""
        for line1, line2 in zip(s1,s2):
            result += f"{line1:{maxlen}}    |    {line2}\n"
        return result
    def loop(self):
        num = 0
        while True:
             user_output = "Доска пользователя:\n\n" + str(self.us.board)
             ai_output = "Доска пользователя:\n\n" + str(self.ai.board)
             print("-" * 70)
             print(Game.vstack(user_output, ai_output))
             if num % 2 == 0:
                 print("-" * 50)
                 print("Ходит игрок")
                 repeat = self.us.move()
             else:
                 print("-" * 50)
                 print("Ходит противник!")
                 repeat = self.ai.move()
             if repeat:
                 num -= 1
             if self.ai.board.count == 7:
                 print("-" * 50)
                 print("Ходит игрок 2")
                 repeat = self.ai.move()
             if repeat:
                 num -= 1
             if self.ai.board.count == 7:
                print("-" * 50)
                print("Игрок выиграл")
                break
             if self.us.board.count == 7:
                print("-" * 50)
                print("Противник выиграл!")
                break
             num += 1

    def start(self):
        self.greet()
        self.loop()

g = Game()
g.start()
