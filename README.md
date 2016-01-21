package Main;

import java.util.Random;
import java.util.logging.Logger;

import Strategy.AiStrategy;
import Strategy.ConsoleInputStrategy;

public class Game {
	private static final Logger logger = Logger.getLogger(Game.class.getName());
	private Player activePlayer;
	private Player passivePlayer;

	public Game(Player player1, Player player2) {
		activePlayer = chooseBetween(player1, player2);
		if (activePlayer == player1) {
			passivePlayer = player2;
		} else {
			passivePlayer = player1;
		}
		activePlayer.drawStartingHand();
		logger.info("" + activePlayer);
		passivePlayer.drawStartingHand();
		passivePlayer.drawCard(); // draw one more for disadvantage
		logger.info("" + passivePlayer);
	}

	public static void main(String[] args) {
		new Game(new Player("Human", new ConsoleInputStrategy()), new Player("CPU", new AiStrategy())).run();

	}

	private void run() {
		while (true) {
			beginTurn();

			if (getWinner() == null) {
				while (activePlayer.canPlay()) {
					activePlayer.playCard(passivePlayer);
				}

				endTurn();
			} else {
				logger.info("win the game " + getWinner());
				break;
			}

		}
	}

	private void endTurn() {
		swapPlayer();

	}

	private void swapPlayer() {
		Player previousPlayer = activePlayer;
		activePlayer = passivePlayer;
		passivePlayer = previousPlayer;
	}

	private void beginTurn() {
		activePlayer.giveManaSlots();
		activePlayer.refillMana();
		activePlayer.drawCard();
		logger.info(activePlayer + " plays turn...");
	}

	private Player getWinner() {
		if (activePlayer.getHealth() <= 0) {
			return passivePlayer;
		} else if (passivePlayer.getHealth() <= 0) {
			return activePlayer;
		} else {
			return null;
		}
	}

	private Player chooseBetween(Player player, Player opponent) {
		Random random = new Random();
		return random.nextBoolean() ? player : opponent;
	}

}
