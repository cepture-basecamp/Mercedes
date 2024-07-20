# Mercedes
import curses
import random

def main(stdscr):
    # Clear screen
    curses.curs_set(0)
    stdscr.nodelay(1)
    stdscr.timeout(100)

    sh, sw = stdscr.getmaxyx()
    w = curses.newwin(sh, sw, 0, 0)

    ship_x = sw//2
    ship_y = sh-2

    alien_x = random.randint(1, sw-2)
    alien_y = 1

    bomb_x = alien_x
    bomb_y = alien_y + 1

    lives = 5
    score = 0

    fire = False
    bullet_x = 0
    bullet_y = 0

    while lives > 0:
        w.clear()
        w.border(0)
        w.addstr(0, 2, f'Score: {score}')
        w.addstr(0, sw-10, f'Lives: {lives}')

        # Draw ship
        w.addch(ship_y, ship_x, 'A')

        # Draw alien
        if alien_y < sh - 1:
            w.addch(alien_y, alien_x, 'V')
        else:
            alien_x = random.randint(1, sw-2)
            alien_y = 1

        # Draw bomb
        if bomb_y < sh - 1:
            w.addch(bomb_y, bomb_x, '*')
        else:
            bomb_x = alien_x
            bomb_y = alien_y + 1

        # Move bomb
        bomb_y += 1

        # Check if bomb hit the ship
        if bomb_y == ship_y and bomb_x == ship_x:
            lives -= 1
            bomb_y = sh

        # Move alien
        alien_y += 1

        # Fire bullet
        if fire:
            w.addch(bullet_y, bullet_x, '|')
            bullet_y -= 1
            if bullet_y == 0:
                fire = False

        # Check if bullet hit the alien
        if fire and bullet_y == alien_y and bullet_x == alien_x:
            score += 10
            alien_x = random.randint(1, sw-2)
            alien_y = 1
            fire = False

        w.refresh()

        key = w.getch()

        if key == curses.KEY_LEFT and ship_x > 1:
            ship_x -= 1
        if key == curses.KEY_RIGHT and ship_x < sw-2:
            ship_x += 1
        if key == curses.KEY_UP and not fire:
            fire = True
            bullet_x = ship_x
            bullet_y = ship_y - 1

    w.clear()
    w.addstr(sh//2, sw//2-5, "GAME OVER")
    w.refresh()
    w.getch()

curses.wrapper(main)
