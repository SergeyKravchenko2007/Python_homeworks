# Python_homeworks
class Game:
    def init(self):
        self.screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
        pygame.display.set_caption("Top-Down Battle Game")
        self.clock = pygame.time.Clock()
        self.running = True
        
        # Створення гравців
        self.players = [
            Player(100, 300, "warrior"),
            Player(100, 200, "archer"),
            Player(100, 400, "tank")
        ]
        
        # Створення воріт
        self.goals = [
            Goal(700, 150, "castle"),
            Goal(700, 300, "base"),
            Goal(700, 450, "portal")
        ]
        
        self.current_player_index = 0
        self.font = pygame.font.SysFont(None, 36)
    
    def handle_events(self):
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                self.running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_TAB:
                    self.current_player_index = (self.current_player_index + 1) % len(self.players)
        
        keys = pygame.key.get_pressed()
        current_player = self.players[self.current_player_index]
        
        dx, dy = 0, 0
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            dx = -1
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            dx = 1
        if keys[pygame.K_UP] or keys[pygame.K_w]:
            dy = -1
        if keys[pygame.K_DOWN] or keys[pygame.K_s]:
            dy = 1
            
        current_player.move(dx, dy)
        
        if keys[pygame.K_SPACE]:
            current_player.attack(self.goals)
    
    def update(self):
        # Перевірка на знищення воріт
        for goal in self.goals[:]:
            if goal.health <= 0:
                self.goals.remove(goal)
        
        # Перевірка на завершення гри
        if len(self.goals) == 0:
            print("Гра завершена! Ви перемогли!")
            self.running = False
    
    def draw(self):
        self.screen.fill((0, 0, 0))  # Чорний фон
        
        # Малювання воріт
        for goal in self.goals:
            goal.draw(self.screen)
        
        # Малювання гравців
        for i, player in enumerate(self.players):
            player.draw(self.screen)
            
            # Позначення поточного гравця
            if i == self.current_player_index:
                pygame.draw.circle(self.screen, (255, 255, 0), (int(player.x), int(player.y - player.size - 20)), 5)
        
        # Інформація про гравця
        current_player = self.players[self.current_player_index]
        player_info = f"Гравець: {current_player.type} | HP: {current_player.health} | Атака: {current_player.attack_power}"
        text_surface = self.font.render(player_info, True, (255, 255, 255))
        self.screen.blit(text_surface, (10, 10))
        
        # Інформація про залишкові ворота
        goals_info = f"Залишилось воріт: {len(self.goals)}"
        text_surface = self.font.render(goals_info, True, (255, 255, 255))
        self.screen.blit(text_surface, (10, 50))
        
        pygame.display.flip()
    
    def run(self):
        while self.running:
            self.handle_events()
            self.update()
            self.draw()
            self.clock.tick(FPS)
        
        pygame.quit()

# Запуск гри
if name == "main":
    game = Game()
    game.run()
