import tkinter as tk
import random
import math

W, H = 900, 650

class SealGame:
    def __init__(self, root):
        self.root = root
        self.root.title("Тюлень Геймс - Максимум Edition 🦭")
        self.root.resizable(False, False)
        self.canvas = tk.Canvas(root, width=W, height=H, bg="#03192e", highlightthickness=0)
        self.canvas.pack()

        # Состояние
        self.state = "menu"  # menu, normal, lol, hard, game_over, egg
        self.score = 0
        self.lives = 3
        self.mode = "normal"
        self.items = []
        self.particles = []
        self.bg_bubbles = []
        self.game_over = False
        self.seal_x = W // 2
        self.frame = 0
        self.is_blinking = False
        self.hit_flash = 0
        self.shake_timer = 0
        self.shake_x = 0
        self.shake_y = 0
        self.boss_active = False
        self.boss_dir = 1
        self.boss_x = W // 2
        self.boss_y = -100
        self.boss_hp = 10
        self.egg_timer = 0

        # Пузырьки фона
        for _ in range(35):
            self.bg_bubbles.append({
                "x": random.randint(0, W),
                "y": random.randint(0, H),
                "r": random.randint(3, 10),
                "speed": random.uniform(0.4, 1.5)
            })

        # Управление
        self.root.bind("<Motion>", self.on_mouse)
        self.root.bind("<Button-1>", self.on_click)
        self.root.bind("<Left>", lambda e: self.move_seal(-30))
        self.root.bind("<Right>", lambda e: self.move_seal(30))
        self.root.bind("<space>", lambda e: self.restart())

        self.show_menu()
        self.loop()

    # ==================== УПРАВЛЕНИЕ ====================
    def on_mouse(self, e):
        if self.state in ["normal", "lol", "hard"] and not self.game_over:
            self.seal_x = max(90, min(W - 90, e.x))

    def move_seal(self, dx):
        if self.state in ["normal", "lol", "hard"] and not self.game_over:
            self.seal_x = max(90, min(W - 90, self.seal_x + dx))

    def on_click(self, e):
        if self.state == "menu":
            if 30 < e.x < 110 and 30 < e.y < 110:
                self.state = "egg"
                self.egg_timer = 150
                return
            if 300 < e.x < 600:
                if 280 < e.y < 350: self.start_game("normal")
                elif 380 < e.y < 450: self.start_game("lol")
                elif 480 < e.y < 550: self.start_game("hard")
        elif self.state == "game_over":
            if 300 < e.x < 600 and 400 < e.y < 470:
                self.show_menu()

    def restart(self):
        if self.state == "game_over":
            self.show_menu()

    def start_game(self, mode):
        self.state = mode
        self.mode = mode
        self.score = 0
        self.lives = 1 if mode == "hard" else 3
        self.items = []
        self.particles = []
        self.game_over = False
        self.seal_x = W // 2
        self.frame = 0
        self.hit_flash = 0
        self.shake_timer = 0
        self.boss_active = False
        self.boss_hp = 10
        self.is_blinking = False

    def show_menu(self):
        self.state = "menu"
        self.game_over = False
        self.boss_active = False

    def lose_life(self):
        self.lives -= 1
        self.hit_flash = 15
        self.shake_timer = 15
        for _ in range(22):
            self.particles.append({
                "x": self.seal_x, "y": 540,
                "vx": random.uniform(-9, 9),
                "vy": random.uniform(-16, -6),
                "life": 1.0, "color": "#ff2a2a",
                "size": random.randint(3, 7)
            })
        if self.lives <= 0:
            self.game_over = True
            self.state = "game_over"

    def spawn_particles(self, color, count=12):
        for _ in range(count):
            self.particles.append({
                "x": self.seal_x, "y": 540,
                "vx": random.uniform(-7, 7),
                "vy": random.uniform(-14, -4),
                "life": 1.0, "color": color,
                "size": random.randint(3, 6)
            })

    # ==================== ИГРОВОЙ ЦИКЛ ====================
    def loop(self):
        if self.state in ["normal", "lol", "hard"] and not self.game_over:
            self.update_game()
        self.redraw()
        self.root.after(16, self.loop)

    def update_game(self):
        self.frame += 1

        # Моргание
        if self.frame % 200 == 0: self.is_blinking = True
        if self.frame % 200 == 15: self.is_blinking = False

        # Тряска
        if self.shake_timer > 0:
            self.shake_timer -= 1
            self.shake_x = random.uniform(-10, 10)
            self.shake_y = random.uniform(-10, 10)
        else:
            self.shake_x = 0
            self.shake_y = 0

        # Вспышка
        if self.hit_flash > 0: self.hit_flash -= 1

        # Пузырьки фона
        for b in self.bg_bubbles:
            b["y"] -= b["speed"]
            if b["y"] < -20:
                b["y"] = H + 20
                b["x"] = random.randint(0, W)

        # Частицы
        for p in self.particles[:]:
            p["x"] += p["vx"]
            p["y"] += p["vy"]
            p["vy"] += 0.55
            p["life"] -= 0.022
            if p["life"] <= 0:
                self.particles.remove(p)

        # Спавн
        spawn_rate = 45 if self.mode == "normal" else 28 if self.mode == "lol" else 20
        if self.frame % spawn_rate == 0:
            x = random.randint(70, W - 70)
            kind = "fish"
            r = random.random()
            if self.mode == "normal":
                if r > 0.82: kind = "trash"
                elif r > 0.90: kind = "star"
                elif r > 0.96: kind = "medkit"
            elif self.mode == "lol":
                if r > 0.65: kind = "demon"
                elif r > 0.90: kind = "star"
            elif self.mode == "hard":
                if r > 0.55: kind = "bomb"
                elif r > 0.82: kind = "star"
                elif r > 0.92: kind = "medkit"
            speed = random.uniform(3.5, 6)
            if self.mode == "hard": speed *= 1.5
            self.items.append({"x": x, "y": -40, "kind": kind, "speed": speed, "angle": random.uniform(0, 6.28)})

        # Босс (каждые ~15 секунд, кроме хардмода)
        if self.mode != "hard" and not self.boss_active:
            if self.frame > 900 and self.frame % 900 == 0:
                self.boss_active = True
                self.boss_x = W // 2
                self.boss_y = -120
                self.boss_hp = 10
                self.boss_dir = 1

        # Движение босса
        if self.boss_active:
            if self.boss_y < 130:
                self.boss_y += 1.5
            else:
                self.boss_x += self.boss_dir * 3
                if self.boss_x < 150 or self.boss_x > W - 150:
                    self.boss_dir *= -1

        # Движение предметов
        for item in self.items[:]:
            item["y"] += item["speed"]
            item["angle"] += 0.1

            # Столкновение с тюленем
            if 540 < item["y"] < 620 and abs(item["x"] - self.seal_x) < 85:
                if item["kind"] == "fish":
                    self.score += 1
                    self.spawn_particles("#2eff6b", 8)
                elif item["kind"] == "star":
                    self.score += 3
                    self.spawn_particles("#ffe84d", 14)
                elif item["kind"] == "medkit":
                    self.lives = min(self.lives + 1, 5)
                    self.spawn_particles("#ffffff", 10)
                else:
                    self.lose_life()
                self.items.remove(item)
                continue

            # Столкновение с боссом (урон)
            if self.boss_active and abs(item["x"] - self.boss_x) < 100 and abs(item["y"] - self.boss_y) < 100:
                if item["kind"] in ["fish", "star"]:
                    self.boss_hp -= 1
                    self.spawn_particles("#ff9933", 8)
                    self.items.remove(item)
                    if self.boss_hp <= 0:
                        self.boss_active = False
                        self.score += 50
                        self.spawn_particles("#ffcc00", 40)
                        self.hit_flash = 20
                    continue

            # Упал за экран
            if item["y"] > H + 60:
                self.items.remove(item)
                if item["kind"] == "fish":
                    self.lose_life()

    # ==================== ОТРИСОВКА ====================
    def redraw(self):
        self.canvas.delete("all")
        sx, sy = self.shake_x, self.shake_y

        # ========== ФОН ==========
        # Глубина
        self.canvas.create_rectangle(-20, -20, W + 20, H + 20, fill="#03192e", outline="")
        # Средний слой воды
        self.canvas.create_rectangle(sx, sy, W + sx, H * 0.6 + sy, fill="#0a2c4d", outline="")
        # Светлая вода сверху
        self.canvas.create_rectangle(sx, sy, W + sx, H * 0.3 + sy, fill="#144a75", outline="")

        # Лучи света сверху
        self.canvas.create_polygon(150+sx, 0+sy, 350+sx, 0+sy, 50+sx, H+sy, -50+sx, H+sy, fill="#2a8ab8", outline="")
        self.canvas.create_polygon(400+sx, 0+sy, 550+sx, 0+sy, 300+sx, H+sy, 200+sx, H+sy, fill="#1f6f9e", outline="")
        self.canvas.create_polygon(700+sx, 0+sy, 850+sx, 0+sy, 700+sx, H+sy, 600+sx, H+sy, fill="#2a8ab8", outline="")

        # Песок
        self.canvas.create_oval(-150+sx, H-80+sy, W*0.55+sx, H+180+sy, fill="#c2a878", outline="")
        self.canvas.create_oval(W*0.45+sx, H-90+sy, W+200+sx, H+180+sy, fill="#b39960", outline="")
        self.canvas.create_oval(-100+sx, H-40+sy, W*0.6+sx, H+150+sy, fill="#d4b88c", outline="")
        self.canvas.create_oval(W*0.4+sx, H-30+sy, W+100+sx, H+150+sy, fill="#dcc296", outline="")

        # Водоросли
        for wx in [60, 180, 320, 520, 660, 780, 850]:
            wave = math.sin(self.frame * 0.03 + wx) * 15
            self.canvas.create_line(wx+sx, H+sy, wx+wave+sx, H-80+sy, wx-wave+sx, H-160+sy,
                                    fill="#1a5c3a", width=8, smooth=True)
            self.canvas.create_line(wx+sx, H+sy, wx-wave+sx, H-90+sy, wx+wave*0.7+sx, H-180+sy,
                                    fill="#2d7a4d", width=5, smooth=True)

        # Пузырьки (анимация)
        for b in self.bg_bubbles:
            bx, by, br = b["x"] + sx, b["y"] + sy, b["r"]
            self.canvas.create_oval(bx - br, by - br, bx + br, by + br,
                                    outline="#bfe6fa", width=1)
            self.canvas.create_oval(bx - br*0.4, by - br*0.4, bx - br*0.1, by - br*0.1,
                                    fill="#ffffff", outline="")

        # ========== КОНТЕНТ ==========
        if self.state == "menu" or self.state == "egg":
            self.draw_menu(sx, sy)
        elif self.state == "game_over":
            self.draw_game_over(sx, sy)
        else:
            self.draw_game(sx, sy)

        # ========== ОБЩИЕ ЭЛЕМЕНТЫ ==========
        # Вспышка
        if self.hit_flash > 0:
            self.canvas.create_rectangle(sx, sy, W + sx, H + sy, fill="#ff0000", stipple="gray25", outline="")

    # ---------- МЕНЮ ----------
    def draw_menu(self, sx, sy):
        # Заголовок
        self.canvas.create_text(W//2 + sx + 3, 133 + sy, text="ТЮЛЕНЬ ГЕЙМС",
                                fill="#000000", font=("Impact", 62, "bold"))
        self.canvas.create_text(W//2 + sx, 130 + sy, text="ТЮЛЕНЬ ГЕЙМС",
                                fill="#00ffff", font=("Impact", 62, "bold"))
        self.canvas.create_text(W//2 + sx, 190 + sy, text="МАКСИМУМ EDITION",
                                fill="#ffffff", font=("Arial", 22, "bold"))

        # Кнопки
        buttons = [
            (280, 350, "#3498db", "#5dade2", "РЫБАЛКА"),
            (380, 450, "#e84118", "#ff6b47", "ЛОЛ-МОД"),
            (480, 550, "#8e44ad", "#b06ed6", "ХАРДМОД")
        ]
        for y1, y2, col, light, text in buttons:
            self.canvas.create_rectangle(300 + sx, y1 + sy, 600 + sx, y2 + sy,
                                         fill=col, outline=light, width=3)
            self.canvas.create_text(450 + sx, (y1 + y2)//2 + sy, text=text,
                                    fill="#ffffff", font=("Arial", 22, "bold"))

        # Пасхалка (рыбка)
        fish_bob = math.sin(self.frame * 0.05) * 5
        self.canvas.create_oval(40 + sx, 70 + fish_bob + sy, 110 + sx, 105 + fish_bob + sy,
                                fill="#FFD700", outline="#FFA500", width=3)
        self.canvas.create_polygon(40 + sx, 87 + fish_bob + sy, 10 + sx, 60 + fish_bob + sy,
                                   10 + sx, 115 + fish_bob + sy,
                                   fill="#FFD700", outline="#FFA500", width=3)
        self.canvas.create_oval(95 + sx, 80 + fish_bob + sy, 103 + sx, 88 + fish_bob + sy,
                                fill="#000000")

        # Пузырь пасхалки
        if self.state == "egg" and self.egg_timer > 0:
            self.egg_timer -= 1
            self.canvas.create_rectangle(120 + sx, 60 + sy, 460 + sx, 130 + sy,
                                         fill="#ffffff", outline="#0b2d45", width=4)
            self.canvas.create_text(290 + sx, 85 + sy,
                                    text="Ты нашёл пасхалку!",
                                    fill="#0b2d45", font=("Arial", 18, "bold"))
            self.canvas.create_text(290 + sx, 110 + sy,
                                    text="Слава тюленям! 🦭",
                                    fill="#0b2d45", font=("Arial", 14))
            if self.egg_timer == 0:
                self.state = "menu"

    # ---------- GAME OVER ----------
    def draw_game_over(self, sx, sy):
        self.canvas.create_rectangle(150 + sx, 100 + sy, W - 150 + sx, H - 100 + sy,
                                     fill="#03192e", outline="#00ffff", width=5)
        self.canvas.create_text(W//2 + sx, 200 + sy, text="ИГРА ОКОНЧЕНА",
                                fill="#ff4d4d", font=("Impact", 48, "bold"))
        self.canvas.create_text(W//2 + sx, 300 + sy, text=f"Твой счёт: {self.score}",
                                fill="#FFD700", font=("Arial", 30, "bold"))
        self.canvas.create_rectangle(300 + sx, 400 + sy, 600 + sx, 470 + sy,
                                     fill="#4cd137", outline="white", width=3)
        self.canvas.create_text(W//2 + sx, 435 + sy, text="В МЕНЮ",
                                fill="white", font=("Arial", 22, "bold"))

    # ---------- ИГРА ----------
    def draw_game(self, sx, sy):
        # Босс
        if self.boss_active:
            bx, by = self.boss_x + sx, self.boss_y + sy
            self.canvas.create_oval(bx - 100, by - 90, bx + 100, by + 90,
                                    fill="#5a0000", outline="#ff0000", width=5)
            # Рога
            self.canvas.create_polygon(bx - 70, by - 70, bx - 100, by - 130, bx - 40, by - 80,
                                        fill="#1a1a1a", outline="#ff0000")
            self.canvas.create_polygon(bx + 70, by - 70, bx + 100, by - 130, bx + 40, by - 80,
                                        fill="#1a1a1a", outline="#ff0000")
            # Глаза
            self.canvas.create_oval(bx - 40, by - 20, bx - 10, by + 10, fill="#ffff00")
            self.canvas.create_oval(bx + 10, by - 20, bx + 40, by + 10, fill="#ffff00")
            self.canvas.create_oval(bx - 30, by - 10, bx - 15, by + 5, fill="#000000")
            self.canvas.create_oval(bx + 15, by - 10, bx + 30, by + 5, fill="#000000")
            # Зубастая пасть
            self.canvas.create_polygon(bx - 40, by + 40, bx + 40, by + 40,
                                        bx + 25, by + 70, bx - 25, by + 70,
                                        fill="#000000", outline="#ff0000", width=3)
            for i in range(-30, 35, 15):
                self.canvas.create_polygon(bx + i, by + 40, bx + i + 7, by + 55, bx + i + 14, by + 40,
                                            fill="#ffffff")
            # HP босса
            hp_width = 180
            self.canvas.create_rectangle(bx - hp_width//2, by - 140,
                                          bx + hp_width//2, by - 125,
                                          fill="#330000", outline="#ff0000")
            self.canvas.create_rectangle(bx - hp_width//2, by - 140,
                                          bx - hp_width//2 + hp_width * (self.boss_hp / 10), by - 125,
                                          fill="#ff3333", outline="")

        # Тюлень
        self.draw_seal(sx, sy)

        # Предметы
        for item in self.items:
            self.draw_item(item, sx, sy)

        # Частицы
        for p in self.particles:
            size = p["size"]
            self.canvas.create_oval(p["x"] - size + sx, p["y"] - size + sy,
                                    p["x"] + size + sx, p["y"] + size + sy,
                                    fill=p["color"], outline="")

        # HUD
        self.draw_hud(sx, sy)

    def draw_seal(self, sx, sy):
        breath = math.sin(self.frame * 0.08) * 4
        sw = 170 + breath
        sh = 150 + breath
        cy = 570
        cx = self.seal_x + sx

        # Тень на песке
        self.canvas.create_oval(cx - 90, cy + 40 + sy, cx + 90, cy + 75 + sy,
                                fill="#000000", stipple="gray50", outline="")

        # Хвост
        tail_wave = math.sin(self.frame * 0.15) * 10
        self.canvas.create_polygon(
            cx + 70 + sx, cy + 20 + sy,
            cx + 130 + tail_wave + sx, cy + 5 + sy,
            cx + 130 + tail_wave + sx, cy + 55 + sy,
            fill="#8fa3b8", outline="#6e8499", width=2
        )

        # Ласты
        self.canvas.create_oval(cx - 110 + sx, cy - 10 + sy, cx - 60 + sx, cy + 50 + sy,
                                fill="#7a8ea3", outline="#6e8499", width=2)
        self.canvas.create_oval(cx + 60 + sx, cy - 10 + sy, cx + 110 + sx, cy + 50 + sy,
                                fill="#7a8ea3", outline="#6e8499", width=2)

        # Тело
        self.canvas.create_oval(cx - sw/2 + sx, cy - sh/2 + sy,
                                cx + sw/2 + sx, cy + sh/2 + sy,
                                fill="#8fa3b8", outline="#6e8499", width=3)
        # Живот
        self.canvas.create_oval(cx - 55 + sx, cy - 30 + sy,
                                cx + 55 + sx, cy + 55 + sy,
                                fill="#e8eff5", outline="")

        # Голова
        self.canvas.create_oval(cx - 80 + sx, cy - 140 + sy,
                                cx + 80 + sx, cy - 10 + sy,
                                fill="#a3b6c8", outline="#6e8499", width=3)

        # Усы
        for dy in [-90, -80, -70]:
            self.canvas.create_line(cx - 75 + sx, cy + dy + sy,
                                    cx - 120 + sx, cy + dy - 5 + sy,
                                    fill="#5a7085", width=2)
            self.canvas.create_line(cx + 75 + sx, cy + dy + sy,
                                    cx + 120 + sx, cy + dy - 5 + sy,
                                    fill="#5a7085", width=2)

        # Раны
        if self.lives < 3:
            # Пластырь
            self.canvas.create_rectangle(cx + 20 + sx, cy - 135 + sy, cx + 60 + sx, cy - 115 + sy,
                                          fill="#e6d5b3", outline="#b3a384", width=2)
            for lx in [30, 40, 50]:
                self.canvas.create_line(cx + lx + sx, cy - 135 + sy,
                                        cx + lx + sx, cy - 115 + sy,
                                        fill="#b3a384", width=1)
        if self.lives == 1:
            # Шрам
            self.canvas.create_line(cx - 55 + sx, cy - 85 + sy,
                                    cx - 30 + sx, cy - 60 + sy,
                                    fill="#cc2929", width=3)

        # Глаза
        if not self.is_blinking:
            if self.lives == 1:
                # Крестики
                for ex, ey in [(-45, -110), (25, -110)]:
                    self.canvas.create_line(cx + ex - 8 + sx, cy + ey - 8 + sy,
                                            cx + ex + 8 + sx, cy + ey + 8 + sy,
                                            fill="#1e2b36", width=4)
                    self.canvas.create_line(cx + ex - 8 + sx, cy + ey + 8 + sy,
                                            cx + ex + 8 + sx, cy + ey - 8 + sy,
                                            fill="#1e2b36", width=4)
            else:
                for ex in [-45, 25]:
                    self.canvas.create_oval(cx + ex - 12 + sx, cy - 122 + sy,
                                            cx + ex + 12 + sx, cy - 98 + sy,
                                            fill="#1e2b36", outline="")
                    self.canvas.create_oval(cx + ex - 8 + sx, cy - 118 + sy,
                                            cx + ex - 2 + sx, cy - 112 + sy,
                                            fill="#ffffff", outline="")

        # Нос
        self.canvas.create_oval(cx - 15 + sx, cy - 65 + sy, cx + 15 + sx, cy - 45 + sy,
                                fill="#2b3a47", outline="")
        self.canvas.create_oval(cx - 10 + sx, cy - 62 + sy, cx - 5 + sx, cy - 57 + sy,
                                fill="#5a7085", outline="")

    def draw_item(self, item, sx, sy):
        ix, iy = item["x"] + sx, item["y"] + sy
        kind = item["kind"]

        if kind == "fish":
            tail_wave = math.sin(item["angle"]) * 6
            # Тело
            self.canvas.create_oval(ix - 22, iy - 12, ix + 22, iy + 12,
                                    fill="#ffa94d", outline="#d97a1a", width=2)
            # Хвост
            self.canvas.create_polygon(
                ix - 20, iy,
                ix - 42 + tail_wave, iy - 18,
                ix - 42 + tail_wave, iy + 18,
                fill="#ffa94d", outline="#d97a1a", width=2
            )
            # Плавник
            self.canvas.create_polygon(ix - 5, iy - 12, ix + 5, iy - 22, ix + 15, iy - 12,
                                        fill="#ffcc66", outline="#d97a1a")
            # Глаз
            self.canvas.create_oval(ix + 10, iy - 5, ix + 16, iy + 1,
                                    fill="#1e2b36", outline="")
            self.canvas.create_oval(ix + 12, iy - 4, ix + 14, iy - 2,
                                    fill="#ffffff", outline="")

        elif kind == "star":
            # Звезда
            pts = []
            for i in range(10):
                ang = math.pi / 5 * i - math.pi / 2 + item["angle"] * 0.3
                r = 22 if i % 2 == 0 else 9
                pts.extend([ix + r * math.cos(ang), iy + r * math.sin(ang)])
            self.canvas.create_polygon(pts, fill="#ffe84d", outline="#ffaa00", width=2)

        elif kind == "medkit":
            self.canvas.create_rectangle(ix - 18, iy - 18, ix + 18, iy + 18,
                                          fill="#ffffff", outline="#888888", width=2)
            self.canvas.create_rectangle(ix - 5, iy - 14, ix + 5, iy + 14,
                                          fill="#ff3333", outline="")
            self.canvas.create_rectangle(ix - 14, iy - 5, ix + 14, iy + 5,
                                          fill="#ff3333", outline="")

        elif kind == "trash":
            self.canvas.create_rectangle(ix - 18, iy - 18, ix + 18, iy + 18,
                                          fill="#6c7a89", outline="#4a555f", width=2)
            self.canvas.create_line(ix - 12, iy - 12, ix + 12, iy + 12,
                                    fill="#4a555f", width=2)
            self.canvas.create_line(ix + 12, iy - 12, ix - 12, iy + 12,
                                    fill="#4a555f", width=2)

        elif kind == "demon":
            # Демоническая рыба
            self.canvas.create_polygon(ix, iy - 22, ix + 22, iy, ix, iy + 22, ix - 22, iy,
                                        fill="#8b1a1a", outline="#cc2929", width=2)
            # Рожки
            self.canvas.create_polygon(ix - 10, iy - 18, ix - 18, iy - 32, ix - 5, iy - 18,
                                        fill="#1a1a1a")
            self.canvas.create_polygon(ix + 10, iy - 18, ix + 18, iy - 32, ix + 5, iy - 18,
                                        fill="#1a1a1a")
            # Глаз
            self.canvas.create_oval(ix - 5, iy - 5, ix + 5, iy + 5, fill="#ffff00", outline="")

        elif kind == "bomb":
            self.canvas.create_oval(ix - 22, iy - 22, ix + 22, iy + 22,
                                    fill="#1a1a1a", outline="#444444", width=2)
            # Блик
            self.canvas.create_oval(ix - 14, iy - 14, ix - 6, iy - 6,
                                    fill="#555555", outline="")
            # Фитиль
            self.canvas.create_line(ix + 10, iy - 20, ix + 22, iy - 40,
                                    fill="#8b6914", width=3)
            # Искра
            sparkle = random.randint(1, 3)
            self.canvas.create_oval(ix + 22 - sparkle, iy - 42 - sparkle,
                                    ix + 22 + sparkle, iy - 42 + sparkle,
                                    fill="#ffaa00", outline="")

    def draw_hud(self, sx, sy):
        # Счёт с рыбкой
        self.canvas.create_oval(20 + sx, 20 + sy, 55 + sx, 45 + sy,
                                fill="#FFD700", outline="#FFA500", width=2)
        self.canvas.create_polygon(20 + sx, 32 + sy, 5 + sx, 15 + sy, 5 + sx, 50 + sy,
                                    fill="#FFD700", outline="#FFA500", width=2)
        self.canvas.create_oval(48 + sx, 27 + sy, 54 + sx, 33 + sy,
                                fill="#000000")
        self.canvas.create_text(75 + sx, 32 + sy, text=str(self.score), anchor="w",
                                fill="#FFD700", font=("Arial", 26, "bold"))

        # Жизни (сердечки)
        for i in range(max(0, self.lives)):
            hx = W - 45 - i * 50 + sx
            hy = 32 + sy
            # Сердечко из двух кругов и треугольника
            self.canvas.create_oval(hx - 16, hy - 14, hx, hy + 2,
                                    fill="#ff3366", outline="#cc1144", width=2)
            self.canvas.create_oval(hx, hy - 14, hx + 16, hy + 2,
                                    fill="#ff3366", outline="#cc1144", width=2)
            self.canvas.create_polygon(hx - 16, hy - 4,
                                        hx, hy + 20,
                                        hx + 16, hy - 4,
                                        fill="#ff3366", outline="#cc1144", width=2)


if __name__ == "__main__":
    root = tk.Tk()
    game = SealGame(root)
    root.mainloop()
