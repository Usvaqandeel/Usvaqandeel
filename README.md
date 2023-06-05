package Controllerclasses;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.Graphics;
import java.awt.Point;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.util.ArrayList;
import java.util.List;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.Timer;
import javax.swing.JLabel;
import javax.swing.JOptionPane;

public class GameController {
    private GamePanel gamePanel;
    private Snake snake;
    private boolean isGameStarted = false;
    private boolean isGameOver;
    private int score;
    private JLabel scoreLabel;

    public GameController() {
        this.snake = new Snake();
        this.gamePanel = new GamePanel();
        this.score = 0;
        this.scoreLabel = new JLabel("Score: " + score);
    }
public void increaseScore(int points) {
    score += points;
    scoreLabel.setText("Score: " + score);
}

    public GamePanel getGamePanel() {
        return gamePanel;
    }

    public void eatFruit() {
        increaseScore(1);
    }

   private boolean isCollisionWithWalls() {
    Point head = snake.getBody().get(0);
    int x = head.x;
    int y = head.y;

    int width = gamePanel.getWidth();
    int height = gamePanel.getHeight();

    return x < 0 || x >= width || y < 0 || y >= height;
}


  private void displayGameOver() {
    // Calculate the score based on the snake's length
    int score = snake.getBody().size() - 1;

    // Show a dialog with the game over message and scores
    JOptionPane.showMessageDialog(gamePanel, "Game Over\nScore: " + score);

    // Reset the game state
    isGameStarted = false;
    isGameOver = false;
    score = 0;
    scoreLabel.setText("Score: " + score);
    snake = new Snake();
}


    public static void main(String[] args) {
        JFrame frame = new JFrame("Snake Game");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(420, 260);

        GameController gameController = new GameController();
        JPanel gamePanel = gameController.getGamePanel();

        frame.getContentPane().add(gamePanel);
        frame.setVisible(true);

        gameController.startGame();
    }

    public void startGame() {
        isGameStarted = true;
        isGameOver = false;
        Timer timer = new Timer(100, new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                if (isGameStarted && !isGameOver) {
                    snake.move();
                    if (isCollisionWithWalls()) {
                        isGameOver = true;
                        displayGameOver(); // Show game over message and scores
                    }
                    gamePanel.repaint();
                }
            }
        });
        timer.start();
    }

    public void snake() {
        throw new UnsupportedOperationException("Not supported yet."); //To change body of generated methods, choose Tools | Templates.
    }

    private class Fruit extends JPanel {
        private Point position;

        public Fruit() {
            position = new Point();
            generateNewPosition();
            setBackground(Color.RED);
            setPreferredSize(new Dimension(10, 10));
        }

        public Point getPosition() {
            return position;
        }

        public void generateNewPosition() {
            int x = (int) (Math.random() * 40);
            int y = (int) (Math.random() * 20);
            position.setLocation(x, y);
        }
    }

    private enum Direction {
        UP, DOWN, LEFT, RIGHT
    }

    private class Snake {
        private List<Point> body;
        private Direction currentDirection;
        private Direction nextDirection;

        public Snake() {
            body = new ArrayList<>();
            body.add(new Point(5, 5));
            currentDirection = Direction.RIGHT;
            nextDirection = Direction.RIGHT;
        }

        public List<Point> getBody() {
            return body;
        }

        public void move() {
            if (currentDirection == Direction.UP) {
                moveUp();
            } else if (currentDirection == Direction.DOWN) {
                moveDown();
            } else if (currentDirection == Direction.LEFT) {
                moveLeft();
            } else if (currentDirection == Direction.RIGHT) {
                moveRight();
            }

            currentDirection = nextDirection;
        }

        private void moveUp() {
            Point head = body.get(0);
            Point newHead = new Point(head.x, head.y - 1);
            body.add(0, newHead);
            body.remove(body.size() - 1);
        }

        private void moveDown() {
            Point head = body.get(0);
            Point newHead = new Point(head.x, head.y + 1);
            body.add(0, newHead);
            body.remove(body.size() - 1);
        }

        private void moveLeft() {
            Point head = body.get(0);
            Point newHead = new Point(head.x - 1, head.y);
            body.add(0, newHead);
            body.remove(body.size() - 1);
        }

        private void moveRight() {
            Point head = body.get(0);
            Point newHead = new Point(head.x + 1, head.y);
            body.add(0, newHead);
            body.remove(body.size() - 1);
        }
    }

    private class GamePanel extends JPanel {
        private Snake snake;
        private Fruit fruit;
        private JLabel scoreLabel;

        public GamePanel() {
            setPreferredSize(new Dimension(400, 200));
            setBackground(Color.WHITE);

            fruit = new Fruit();
            scoreLabel = new JLabel("Score: " + score);
            snake = new Snake();

            setFocusable(true);
            setLayout(null); // Use null layout for custom component positioning
            add(scoreLabel);
            addKeyListener(new GameKeyListener());

            fruit.generateNewPosition();
            addFruit();

            Timer timer = new Timer(100, new ActionListener() {
                public void actionPerformed(ActionEvent e) {
                    snake.move();
                    checkCollision();
                    repaint();
                }
            });
            timer.start();
        }

        protected void paintComponent(Graphics g) {
            super.paintComponent(g);
g.setColor(Color.RED);
        g.fillRect(0, 0, getWidth(), 10); // Top wall
        g.fillRect(0, getHeight() - 10, getWidth(), 10); // Bottom wall
        g.fillRect(0, 0, 10, getHeight()); // Left wall
        g.fillRect(getWidth() - 10, 0, 10, getHeight());
            g.setColor(Color.GREEN);
            for (Point point : snake.getBody()) {
                int x = point.x * 10;
                int y = point.y * 10;
                g.fillRect(x, y, 10, 10);
            }
        }

        private void checkCollision() {
    Point head = snake.getBody().get(0);
    Point fruitPosition = fruit.getPosition();
    if (head.equals(fruitPosition)) {
        snake.getBody().add(new Point(fruitPosition));
        fruit.generateNewPosition();
        increaseScore(10); // Increase the score by 10 (you can adjust the points as needed)
        addFruit();
    }

    if (isCollisionWithWalls()) {
        isGameOver = true;
        displayGameOver();
    }
}


        private void addFruit() {
            int x = fruit.getPosition().x * 10;
            int y = fruit.getPosition().y * 10;
            fruit.setBounds(x, y, 10, 10);
            add(fruit);
            fruit.repaint(); // Repaint the fruit component after adding it to the panel
        }

        private class GameKeyListener extends KeyAdapter {
            public void keyPressed(KeyEvent e) {
                int keyCode = e.getKeyCode();

                if (keyCode == KeyEvent.VK_UP && snake.currentDirection != Direction.DOWN) {
                    snake.nextDirection = Direction.UP;
                } else if (keyCode == KeyEvent.VK_DOWN && snake.currentDirection != Direction.UP) {
                    snake.nextDirection = Direction.DOWN;
                } else if (keyCode == KeyEvent.VK_LEFT && snake.currentDirection != Direction.RIGHT) {
                    snake.nextDirection = Direction.LEFT;
                } else if (keyCode == KeyEvent.VK_RIGHT && snake.currentDirection != Direction.LEFT) {
                    snake.nextDirection = Direction.RIGHT;
                }
            }
        }
    }
}
