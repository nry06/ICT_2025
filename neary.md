import random
import json
import os

HIGHSCORE_FILE = os.path.join(os.path.dirname(__file__), "guess_highscores.json")


def load_highscores():
    try:
        with open(HIGHSCORE_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except Exception:
        return {}


def save_highscores(data):
    try:
        with open(HIGHSCORE_FILE, "w", encoding="utf-8") as f:
            json.dump(data, f, indent=2)
    except Exception:
        pass


def choose_difficulty():
    print("Choose difficulty:")
    print("  1) Easy   (1-10)")
    print("  2) Medium (1-50)")
    print("  3) Hard   (1-100)")
    while True:
        choice = input("Enter 1-3 (default 2): ").strip() or "2"
        if choice in ("1", "2", "3"):
            if choice == "1":
                return 10
            if choice == "2":
                return 50
            return 100
        print("Please enter 1, 2, or 3.")


def get_int(prompt, low, high):
    while True:
        s = input(prompt).strip()
        try:
            v = int(s)
            if v < low or v > high:
                print(f"Please enter a number between {low} and {high}.")
                continue
            return v
        except ValueError:
            print("That's not a valid integer. Try again.")


def play_round(player_name, max_num):
    secret = random.randint(1, max_num)
    attempts = 0
    print(f"I'm thinking of a number between 1 and {max_num}. Good luck, {player_name}!")
    while True:
        guess = get_int("Your guess: ", 1, max_num)
        attempts += 1
        if guess < secret:
            print("Too low.")
        elif guess > secret:
            print("Too high.")
        else:
            print(f"Correct! You guessed it in {attempts} attempt{'s' if attempts!=1 else ''}.")
            return attempts


def main():
    print("=== Guess The Number ===")
    name = input("Enter your name (or press Enter for 'Player'): ").strip() or "Player"
    highscores = load_highscores()
    best = highscores.get(name)
    if best:
        print(f"Welcome back {name}! Your best is {best} attempts.")
    else:
        print(f"Welcome {name}!")

    while True:
        max_num = choose_difficulty()
        attempts = play_round(name, max_num)

        # update highscores: lower attempts is better
        prev = highscores.get(name)
        if prev is None or attempts < prev:
            highscores[name] = attempts
            save_highscores(highscores)
            print("New personal best! Saved to highscores.")
        else:
            print(f"Your personal best remains {prev} attempts.")

        again = input("Play again? (y/N): ").strip().lower()
        if again != "y":
            print("Thanks for playing — goodbye!")
            break


if __name__ == "__main__":
    main()
