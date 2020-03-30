# 飞机大战代码：显示玩家飞机

```python
import pygame
import time

def main():
    """游戏主程序"""
    # 创建窗口
    screen = pygame.display.set_mode((480, 700), 0, 32)

    # 加载背景图片
    bg = pygame.image.load('feiji/background.png')

    # 加载玩家飞机图片
    hero = pygame.image.load('feiji/hero1.png')

    # 绘制背景图片
    screen.blit(bg, (0, 0))

    # 绘制玩家飞机
    screen.blit(hero, (190, 550))

    # 刷新界面
    pygame.display.update()

    time.sleep(600)  # 冻结代码，单位为 秒


if __name__ == '__main__':
    main()

```