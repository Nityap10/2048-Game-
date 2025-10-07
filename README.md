import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

/**
 *
 * @author 
 */
public class MyProgram implements Runnable, ActionListener, KeyListener
{
    //Window Size variables
    final int WIDTH = 400;
    final int HEIGHT = 500;
    final String TITLE = "My Program";
   
    //Size of grid in game 
    int grid_Size = 4;
   
    //Buttons and Board 
    JButton[][] buttons;
    int[][] board;
    //Score tracking and displays    
    int score;
    JLabel scoreLabel;
  
    // Class Variables  
    JFrame frame;
    JPanel mainPanel;
    
    //variables to track which arrow keys are pressed
    boolean leftPressed = false;
    boolean rightPressed = false;
    boolean upPressed = false;
    boolean downPressed = false;
    
    // Method to assemble our GUI
    public void run()
    {
        // Creates a JFrame (the window for our program)
        frame = new JFrame(TITLE);
        
        // creates a panel to add all the elements to
        mainPanel = new JPanel();
        // sets the layout to none so we can place everything ourselves
        mainPanel.setLayout(null);
        // sets the size of the panel to the size set above
        mainPanel.setPreferredSize(new Dimension(WIDTH,HEIGHT));
        
        //intialize score and score label
        score = 0;
        scoreLabel = new JLabel("Score: 0");
        scoreLabel.setBounds(0, 0, WIDTH, 50);
        scoreLabel.setHorizontalAlignment(JLabel.CENTER);
        mainPanel.add(scoreLabel);
                
        // intialize buttons and board arrays
        buttons = new JButton[grid_Size][grid_Size];
        board = new int[grid_Size][grid_Size];
       
       //create seperate panel for the game grid
        JPanel gridPanel = new JPanel(new GridLayout(grid_Size,grid_Size));
        gridPanel.setBounds(0, 50, WIDTH, WIDTH);
       
       //set up board and add buttons to the grid panel
        setUpBoard();
        addButtons(gridPanel);
        mainPanel.add(gridPanel);
        
        //Create New Game Button
        JButton newGameButton = new JButton("New Game");
        newGameButton.setBounds(50,460,120,30);
        newGameButton.addActionListener(this);
        newGameButton.setActionCommand("New Game");
        mainPanel.add(newGameButton);
        
        //Create Rules Button
        JButton rulesButton = new JButton("Rules");
        rulesButton.setBounds(200,460,120,30);
        rulesButton.addActionListener(this);
        rulesButton.setActionCommand("Rules");
        mainPanel.add(rulesButton);
        
        // add the main panel to the fram to see it
        frame.add(mainPanel);
        
        // Makes the X button close the program
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        // shows the window
        frame.setVisible(true);
        // makes the window size enoguh to fit everything added to it
        // this should always be one of the last lines for GUI programs
        frame.pack();
        
        //add key bindings and start a new game
        addKeyBindings();
        startNewGame();
        
        // check if player won the game
        showWinningMessage();
    }
    
    // method to start a new game
    public void startNewGame() 
    {
        setUpBoard(); // intialize board with zeroes
        addNewTile(); // add first random tile
        addNewTile(); // add second random tile
        updateBoard(); // update display to show the boards state
        score = 0; // reset the score
        updateScore(); // update the score display
        mainPanel.requestFocusInWindow(); 
    }
    
    // method to set up board by setting all tiles to 0
    public void setUpBoard() 
    {
        for(int i = 0; i < grid_Size; i++) // for each row set box = 0
        {
            for(int b = 0; b < grid_Size;b++) // for each column set box = 0
            {
                board[i][b] = 0; 
            }
        }
    }
    
    // method to add buttons to the grid panel and to disable them
    public void addButtons(JPanel gridPanel)
    {
        for(int i = 0; i < grid_Size; i++)
        {
            for(int b = 0 ; b < grid_Size; b++)
            {
                buttons[i][b] = new JButton("");
                buttons[i][b].setEnabled(false); // disable button
                gridPanel.add(buttons[i][b]); // add button to grid panel
            }
        }
    }
    
    // method to add key bindings for arrow keys
    public void addKeyBindings()
    {
        mainPanel.addKeyListener(this);
        mainPanel.setFocusable(true);
        mainPanel.requestFocusInWindow();
    }
    
    // method for key pressed events
    public void keyPressed(KeyEvent e)
    {
        handleKeyPressed(e.getKeyCode());
        performActionBasedOnKey();
    }
    
    // method for key released events
    public void keyReleased(KeyEvent e)
    {
        handleKeyRelease(e.getKeyCode());
    }
    
    // methods for key typed events (not used)
    public void keyTyped(KeyEvent e)
    {
        //not used
    }
    
    // method to handle key pressed events
    public void handleKeyPressed(int keyCode)
    {
            if(keyCode == KeyEvent.VK_RIGHT) 
            {
                rightPressed = true;
            }
            else if(keyCode == KeyEvent.VK_UP)
            {
                upPressed = true;
            }
            else if(keyCode == KeyEvent.VK_LEFT)
            {
                leftPressed = true;
            }
            else if(keyCode == KeyEvent.VK_DOWN)
            {
                downPressed = true;
            }
            performActionBasedOnKey();
        }
    
    // method to handle key released events
    public void handleKeyRelease(int keyCode)
    {
        if(keyCode == KeyEvent.VK_LEFT)
        {
            leftPressed = false;
        }
        else if(keyCode == KeyEvent.VK_RIGHT)
        {
            rightPressed = false;
        }
        else if(keyCode == KeyEvent.VK_UP)
        {
            upPressed = false;
        }
        else if(keyCode == KeyEvent.VK_DOWN)
        {
            downPressed = false;
        }
    }
    
    // method to do actions based on the keys pressed
    public void performActionBasedOnKey()
    {
        if(leftPressed)
        {
            moveLeft();
        }
        if(rightPressed)
        {
            moveRight();
        }
        if(upPressed)
        {
            moveUp();
        }
        if(downPressed)
        {
            moveDown();
        }
        
        checkGameOver();
        updateBoard();
       
        if(didYouWin())
        {
            showWinningMessage();
        }
    }
    
    // method to update game board based on board array
    public void updateBoard()
    {
        for(int i = 0; i < grid_Size; i++)
        {
            for(int b = 0; b < grid_Size; b++) 
            {
                // check if value in board array is 0
                if(board[i][b] == 0) 
                {
                    buttons[i][b].setText(""); // set button text to empty
                }
                else
                {
                    buttons[i][b].setText(String.valueOf(board[i][b])); // set button text to value in the board
                }
            }
        }
        updateScore(); // update score display after updating board
    }
    
    // method to update score label
    public void updateScore()
    {
        scoreLabel.setText("Score:"+ score);
    }
    
    // method to add a new tile (2 or a 4) to random empty position on board
    public void addNewTile()
    {
        int x = -1;  
        int y = -1; 
        
        // find a random empty position on the board
        while(x == -1 || y ==-1|| board[x][y] !=0) //searching for (board[x][y] ==0)
        {
            x = (int)(Math.random()*grid_Size); // find a row
            y = (int)(Math.random()*grid_Size); // find a column
        }
        
        // set new tile to 2 (90% chance) or a 4 (10% chance)
        if(Math.random() < 0.9) // find value of box (2 or 4)
        {
            board[x][y] = 2; //90% chance to place 2
        }
        else 
        {
            board[x][y] = 4; // 10% chance to place 4
        }
    }
    
    // method to move tiles left
    public void moveLeft()
    {
        boolean moved = false; // check if any tiles moved
        for(int i = 0; i < grid_Size; i++) 
        {
            int[] newRow = new int[grid_Size]; // create new array to store the non-zero values 
            int position = 0; // position in new row array
            
            //move non-zero tiles to the left
            for(int b = 0; b < grid_Size; b++) 
            {
                if(board[i][b] != 0) 
                {
                    newRow[position++] = board[i][b]; 
                }
            }
            
            // merge adjacent tiles with the same value 
            for(int b = 0; b < position - 1; b++) 
            {
                if(newRow[b] == newRow[b+1]) 
                {
                    newRow[b] *= 2; // merge by doubling value
                    newRow[b + 1] = 0; // clear next tile
                    score += newRow[b]; // increase score by merge value
                    moved = true; // indicate that move has occured
                }
            }
            // move tiles left again after merging
            int[] finalRow = new int[grid_Size]; //create a final row array
            position = 0; // reset position
            for(int b = 0; b < grid_Size; b++) 
            {
                if(newRow[b] != 0) 
                {
                    finalRow[position++] = newRow[b]; 
                }
            }
            
            //update board with final row
            if(!arrayEquals(board[i], finalRow)) 
            {
                moved = true; 
                board[i] = finalRow;
            }
        }
        //add a new tile if a move occured
        if(moved) 
        {
            addNewTile(); 
        }
    }
    
    // method to move tiles right
    public void moveRight()
    {
        boolean moved = false; // check if any tiles were moved
        for(int i = 0; i < grid_Size; i++)
        {
            int[] newRow = new int[grid_Size]; // create new row array to store non-zero values
            int position = grid_Size-1; // start from the rightmost position in the new row array
            
            // move non-zero tiles to the right
            for(int b = grid_Size - 1; b >= 0; b--) 
            {
               if(board[i][b] != 0) 
               {
                   newRow[position--] = board[i][b];
               }
            }
            
            // merge adjacent tiles with the same value
            for(int b = grid_Size -1; b > 0; b--)
            {
                if(newRow[b] == newRow[b - 1])
                {
                    newRow[b] *= 2; // merge by doubling the value
                    newRow[b - 1] = 0; // clear next tiles
                    score+= newRow[b]; // increase score based on merge value
                    moved = true; // indicate that a move has been made
                }
            }
            
            // move tiles right, after merging again
            int[] finalRow = new int[grid_Size]; // create a final row array
            position = grid_Size - 1; // reset position
            for(int b = grid_Size - 1; b >= 0; b--) 
            {
                if(newRow[b] != 0) 
                {
                    finalRow[position--] = newRow[b];
                }
            }
            
            // update board with final row
            if(!arrayEquals(board[i],finalRow)) 
            {
                moved = true;
                board[i] = finalRow; 
            }
        }
        // add a new tile if move occured
        if(moved)
        {
            addNewTile(); 
        }
    }
   
    // method to move tiles up
    public void moveUp()
    {
        boolean moved = false; // check if any tiles were moved
        for(int b = 0; b < grid_Size; b++) 
        {
            int[] newColumn = new int[grid_Size]; // create a new column array to store non-zero values
            int position = 0; // position in the new column array
            
            // move non-zero tiles up
            for(int i = 0; i < grid_Size; i++) 
            {
                if(board[i][b] != 0) 
                {
                    newColumn[position++] = board[i][b];
                }
            }
            // merge adjacent tiles with same value
            for(int i = 0; i < position - 1; i++)
            {
                if(newColumn[i] == newColumn[i + 1])
                {
                    newColumn[i] *= 2; // merge by doubling the value
                    newColumn[i + 1] = 0; // clear the next tile
                    score += newColumn[i]; // increase score based on merge value
                    moved = true; // indicate that a move was made
                }
            }
            
            // move tiles up after merging
            int[] finalColumn = new int[grid_Size]; // create a final column array
            position = 0; // reset position
            
            for(int i = 0; i < grid_Size; i++)
            {
                if(newColumn[i] != 0) 
                {
                    finalColumn[position++] = newColumn[i];
                }
            }
            
            // update board with final column
            if(!columnEquals(b,finalColumn))
            {
                moved = true;
                for(int i = 0; i < grid_Size; i++)
                {
                    board[i][b] = finalColumn[i]; 
                }
            }
            
        }
        // add a new tile if any move occured
        if(moved) 
        {
            addNewTile(); 
        }
    }
    // method to move tiles down
    public void moveDown()
    {
        boolean moved = false; // check if any tiles were moved 
        for(int b = 0; b < grid_Size; b++) 
        {
            int[] newColumn = new int[grid_Size]; // create a new column array to store non-zero values
            int position = grid_Size - 1; // start from bottommost position in new column array
           
            //move non-zero values down
            for(int i = grid_Size - 1; i >= 0; i--) 
            {
                if(board[i][b] != 0) 
                {
                    newColumn[position--] = board[i][b];
                }
            }
            
            //merge adjacent tiles with same value
            for(int i = grid_Size -1; i > 0; i--)
            {
                if(newColumn[i] == newColumn[i - 1])
                {
                    newColumn[i] *= 2; // merge by doubling the value
                    newColumn[i-1] = 0; // clear the next tile
                    score += newColumn[i]; //increase the score based on merge value
                    moved = true;//Row indicate that a move has been made
                }
            }
            
            // move tiles down after merging
            int[] finalColumn = new int[grid_Size];
            position = grid_Size - 1; 
            for(int i = grid_Size - 1; i >= 0; i--)
            {
                if(newColumn[i] != 0) 
                {
                    finalColumn[position--] = newColumn[i];
                }
            }
          
          // update board with final column  
            if(!columnEquals(b,finalColumn))
            {
                moved = true;
                for(int i = 0; i < grid_Size; i++)
                {
                    board[i][b] = finalColumn[i]; 
                }
            }
        }
        // add a new tile if any moves occured
        if(moved) 
        {
            addNewTile(); 
        }
    }
    
    // method to check if two arrays are equal
    public boolean arrayEquals(int[] array1, int[] array2) 
    {
        if(array1.length != array2.length) // are values in box different
        {
            return false;
        }
        
        for(int i = 0; i < array1.length; i++) //check every item in array to check if equal or not
        {
            if(array1[i] != array2[i])
            {
                return false;
            }
        }
        return true;
    }
    
    // method to check if a column in the board is equal to an array
    public boolean columnEquals(int columnIndex, int[] columnArray)
    {
        for(int i = 0; i < grid_Size; i++)
        {
            // compare elements in column of the current row with the items in the columnArray
            if(board[i][columnIndex] != columnArray[i])
            {
                // if any item does not match, return false
                return false;
            }
        }
        // if all items match, return true
        return true;
    }
    
    // method to check if the game is over
    public void checkGameOver()
    {
     boolean hasZero = false; // check if there is an empty space on board
     
        //loop through each cell in board to check for empty spot
        for(int i = 0; i < grid_Size; i++) // check every row
        {
            for(int b = 0; b < grid_Size; b++) // check every column
            {
                if(board[i][b] == 0) // if box is empty it will equal to 0
                {
                    hasZero = true; 
                }
            }
        }
        // if there are no empty spaces on board
        if(!hasZero)
        {
            boolean hasMoves = false; // check for valid moves
            // check adjacent boxes for possible merges
                for(int i = 0; i < grid_Size - 1; i++) 
                {
                    for(int b = 0; b < grid_Size - 1; b++)
                    {
                        // check if current box can merge with box below or to the right of it
                        if(board[i][b] == board[i + 1][b] || board[i][b] == board[i][b + 1])
                        {
                            hasMoves = true;
                        }
                    }
                }
                // if not valid moves are left display a you lost message
                if(!hasMoves)
                {
                    JOptionPane.showMessageDialog(frame,"Game over!. Your Score was "+ score + ". New game?");
                }
        }
    }
    
    // checks if a tile = 2048
    public boolean didYouWin()
    {
        for(int i = 0; i < board.length; i++)
        {
            for(int b = 0; b < board[i].length; b++)
            {
                if(board[i][b] == 2048) 
                {
                    return true;
                }
            }
        }
        return false;
    }
    
    //if won, displays a message
    public void showWinningMessage()
    {
        if(didYouWin()) 
        {
            String message = "YAY YOU WIN!";
            JOptionPane.showMessageDialog(null,message);
        }
    }


    //method called when a button is pressed
     public void actionPerformed(ActionEvent e)
    {
        String command = e.getActionCommand(); // if new game button is clicked, start new game
        if(command.equals("New Game"))
        {
            startNewGame();
        }
        else if(command.equals("Rules")) // rules button = show rules popup
        {
            String message = "1. Use the arrow keys to move the tiles. \n" +
            "2. 2 tiles with same number touch, they merge! \n" +
            "I hope you HAVE FUN!";
            JOptionPane.showMessageDialog(frame, message);
        }
    }
    
    
    // Main method to start our program
    public static void main(String[] args)
    {
        // Creates an instance of our program
        MyProgram gui = new MyProgram();
        // Lets the computer know to start it in the event thread
        SwingUtilities.invokeLater(gui);
    }
}
