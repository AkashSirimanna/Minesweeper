# Minesweeper
Spin-off on the classic game, minesweeper created with Java


/***************************************************************************************
 *                                                                                     
 * Author: Akash Sirimanna                                                              
 * Date:    March 26, 2018                                                           
 * Name:    MinesweeperTemplate                                                        
 * Updated by:  Akash Sirimanna                                                                
 * Description : DangerZone is a remake of the popular retro game "Minesweeper". 
 *               The goal of the game is to clear a
 *					  game board by avoiding mines. Good luck!                                                      
 **************************************************************************************/

//Import statements 
import javax.swing.*;      //Graphics
import java.awt.*;         //Window toolkit
import java.awt.event.*;   
import java.util.Random;	//For mines
import java.util.Arrays;		//For mines/buttons
  
  
 /****************************************************************
* The MinesweeperAkash class contains the main method which creates the                   *
*	Minesweeper object and instantiates the game.																		       *
*****************************************************************/ 
public class MinesweeperAkash
{
   public static void main (String[] args) throws Exception 
   {
   
      Minesweeper minesweeper = new Minesweeper();       //creates minesweeper object
      minesweeper.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
   }
}


/***************************************************************************************
* The Minesweeper class creates the JFrame object and contains all methods to be called*
* within the game. 
****************************************************************************************/
class Minesweeper extends JFrame implements ActionListener
{
   //CONSTANT VARIABLES IN PROGRAM
   public static final int COL = 8;
   public static final int ROW = 8;
   public static final int NUM_MINES = 9;
   public static final int MINE = 9;
   public static final boolean LOST = false;
   public static final boolean WON = true;
   public boolean flagProc = false;                //triggers whenever user rightclicks
   public static final int NUM_FLAGS = NUM_MINES;
   
  //GLOBAL VARIABLES IN PROGRAM
   public Color buttonColour;  
   int[][] mines = new int[ROW][COL];   //For mines
   int[][] altmines = new int[ROW][COL]; //To track bombs after flags are placed
   JButton[][] buttons = new JButton[ROW][COL];    //Board of buttons 
   public static ImageIcon flag = new ImageIcon("flag.png");
   public static ImageIcon bomb = new ImageIcon("bomb.png");
   public static int flagCounter = 0;                 //to track flags placed

  
/******************************************************************************************
* The createMines program initializes the mines array andplaces the number of 				 
* mines in an int[]. The minesare also placed within the altmines array for use with flags. 
*******************************************************************************************/                                            
   public void createMines()
   {    
      Random rand = new Random();                                      
      for (int n = 0; n < NUM_MINES; n++){     //creates the mines in array                                            
         int x = rand.nextInt(COL);
         int y = rand.nextInt(ROW); 
         if (mines[y][x] != 9){
            mines[y][x] = 9;
            altmines[y][x] = 9;                //as a backup when flag is placed on a bomb
         }
         else{
            n--;							         //In case the index already contains a mine.
         }
      }  
   }
   
   
  /**********************************************************************************
  * The countMines method numbers the danger level of each box. The method creates a    
  * duplicate array of mines which has a empty "border" around it. The program will          
  * not give OutOfBoundsExceptions when called.																					
  ***********************************************************************************/ 
   public void countMines()
   {
   
   //creates duplicate array
      int[][] duplicate = new int[ROW+2][COL+2];        
      for (int i = 0 ; i < ROW+2 ; i++){
         for (int j = 0 ; j < COL+2 ; j++)    {
            duplicate[i][j] = 0;
         }
      }
   	
   	//equals duplicate array to mines
      for (int i = 0 ; i < ROW ; i++){
         for (int j = 0 ; j < COL ; j++){   
            duplicate[i+1][j+1] = mines[i][j]; //This method uses more memory but was interesting to figure out
         }
      }
   				
   	//Numbers the danger level of each array index
      for (int i = 1; i < ROW+1; i++){
         for (int n = 1; n < COL+1; n++){
            if (duplicate[i][n] < 9){
               if (duplicate[i-1][n-1] == 9){  //top left
                  mines[i-1][n-1]++;
               }
               if (duplicate[i][n-1] == 9){    //left
                  mines[i-1][n-1]++;
               }
               if (duplicate[i+1][n-1] == 9){  //bottom left
                  mines[i-1][n-1]++;
               }
               if (duplicate[i-1][n] == 9){    //top
                  mines[i-1][n-1]++;
               }
               if (duplicate[i+1][n] == 9){    //bottom
                  mines[i-1][n-1]++;
               }
               if (duplicate[i-1][n+1] == 9){  //top right
                  mines[i-1][n-1]++;
               }  
               if (duplicate[i][n+1] == 9){    //right
                  mines[i-1][n-1]++;
               }
               if (duplicate[i+1][n+1] == 9){  //bottom right
                  mines[i-1][n-1]++;
               }
            }
         }
      }
   }	   		

   

/************************************************************************************
* The minesweeper constructor method creates the board of buttons and sets the game 
   up. The createMines and countMines methods are called and the windows are created  
	and the buttons are added to the pane. The constructor also includes right-click           
	functionality with MouseEvent.    																										    													                                                                                  *
*************************************************************************************/ 
   Minesweeper()
   {
      //Generates mines and counts the danger level
      createMines(); 
      countMines();   
   
      //used for resetting game
      buttonColour = getBackground();
            
      // PHYSICAL BOARD CREATION
      setTitle("DangerZone");
      JPanel pane = (JPanel) getContentPane();
      pane.setLayout(new GridLayout(ROW,COL)); //sets pane to desired layout
      
      //Creates buttons, adds action and mouse event to buttons, adds buttons to pane.
      for (int i = 0 ; i < ROW ; i++){
         for (int j = 0 ; j < COL ; j++){
            buttons[i][j] = new JButton("");  
            buttons[i][j].addActionListener(this);
            pane.add(buttons[i][j]);
           
            //FLAG FUNCTIONALITY METHODS (Allows user to rightclick and place flag)
            buttons[i][j].addMouseListener(
                      new MouseListener() {
                         public void mousePressed(MouseEvent me) { 
                            JButton bPressed = (JButton)me.getSource();
                            if(me.getButton() == MouseEvent.BUTTON3) {
                               flagProc = true;      //triggers event in actionPerformed                       
                               bPressed.doClick();   //"calls" actionPerformed
                            }
                         }
                         public void mouseReleased(MouseEvent me) { }
                         public void mouseEntered(MouseEvent me) { }
                         public void mouseExited(MouseEvent me) { }
                         public void mouseClicked(MouseEvent me) { 
                         }
                      });
         }
      }
   	
      //Board is displayed on screen
      pack();
      setLocationRelativeTo(null);   // location
      setSize(70*COL,70*ROW);    // width, height
      setVisible(true);
   } 
 
 
/**********************************************************************************************
*  The checkIfWon method transverses through the buttons array and checks what          
*	buttons are enabled. It also checks if there are flags. If the number of "enabled" buttons
*	are equal to the (number of mines - number of flags placed), the game is won.						
***********************************************************************************************/ 
   public void checkIfWon()
   {
      int countEnabledButtons = 0;
      for (int i = 0; i < ROW; i++){
         for (int j = 0; j < COL; j++){
            if (buttons[i][j].isEnabled() && mines[i][j] != 10)			//checks for enabled buttons
               countEnabledButtons++;											
         }
      } 
      if (countEnabledButtons == NUM_MINES - flagCounter)		//checks if the game is won
         endMessage(WON);
   }


/****************************************************************
* The endMessage method displays a message to the user          *
* based on an boolean argument. The method also calls           *
* resetGame, which may or may not restart the game (based on input   															 
*****************************************************************/ 
   public void endMessage(boolean won)
   {
      for (int i = 0; i < ROW; i++)             //to reveal all bombs as bombs
         for (int j = 0; j < COL; j++){
            if (altmines[i][j] == 9){            //use altmines just incase flags are on bombs
               buttons[i][j].setIcon(bomb);
               buttons[i][j].setBackground(Color.RED);
               }
         }	
      if (won){
         JOptionPane.showMessageDialog(null,"Well Done!");  //game won message
      }
      else{
         JOptionPane.showMessageDialog(null,"Good Try!");   //game lost message
      }
      int choice = JOptionPane.showConfirmDialog(null, "Play Again?","Play Again",JOptionPane.YES_NO_OPTION);
      if (choice == JOptionPane.YES_OPTION){
      resetGame();
      }  

           else{
         JOptionPane.showMessageDialog(null, "Goodbye");         //goodbye message, game end
         System.exit(0);
      } 
   }


/****************************************************************
* The resetGame method asks user if they want to play again     *
* Based on user input, the method will either reset the gameboard
* or display "goodbye"                                          *      															 
*****************************************************************/ 
   public void resetGame()
   {
         for (int i = 0 ; i < ROW ; i++){          //resets gameboard
            for (int j = 0 ; j < COL ; j++){
               mines[i][j] = 0;                   //resets arrays
               altmines[i][j] = 0;
               buttons[i][j].setEnabled(true); 
               buttons[i][j].setText("");
               buttons[i][j].setBackground(buttonColour);
               buttons[i][j].setIcon(null);
            }
         }
         createMines();    
         countMines(); 
         flagCounter = 0;	                                           	
   }
    
	 
/*****************************************************************
* The smallCheck method is a method used for flag functionality  *
* This method serves the same purpose as countMines, but only for*
* one mine. This is to reassure the danger value after           *
* placing/removing a flag.                                       *                                             															 
*****************************************************************/ 
   void smallCheck(int i, int j){
      mines[i][j] = 0;
      if (altmines[i][j] != 9){
         try {
            if (altmines[i-1][j-1] == MINE)     //top left
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
         try{     
            if (altmines[i-1][j] == MINE)      //top
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
         try {
            if (altmines[i-1][j+1] == MINE)    //top right
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
         try{
            if (altmines[i][j-1] == MINE)     //left
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
         try{
            if (altmines[i][j+1] == MINE)    //right
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
         try{
            if (altmines[i+1][j-1] == MINE)  //bottom left
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
         try{
            if (altmines[i+1][j] == MINE)    //bottom
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
         try{
            if (altmines[i+1][j+1] == MINE)  //bottom right
               mines[i][j]++;
         }
         catch(Exception e ){					
         }
      }
      else{    
         mines[i][j] = 9;         //just incase the index is a bomb
      }
   }
    
    
 /*****************************************************************
* openUpBlanks (best name ever) is a method with a recursive    *
* nature. It has the purpose of opening up the gameboard if blank
* spaces are adjacent to the clicked button. openUpBlanks calls *
* itself to efficiently open the gameboard by disabling and     *
* colouring buttons. It also reveals unblank buttons.(Base case)*                                   															 
*****************************************************************/ 
   public void openUpBlanks(int i, int j)
   {
      try{
         if (mines[i][j] == 0 && buttons[i][j].isEnabled()){   //if spot is a blank and enabled
            buttons[i][j].setBackground(Color.WHITE);          
            buttons[i][j].setEnabled(false); 
            openUpBlanks(i-1,j-1);                            //recursive calls to check around
            openUpBlanks(i,j-1);
            openUpBlanks(i+1,j-1);
            openUpBlanks(i-1,j);
            openUpBlanks(i+1,j);
            openUpBlanks(i-1,j+1);
            openUpBlanks(i,j+1);
            openUpBlanks(i+1,j+1);
         }
         else if (mines[i][j] > 0 && mines[i][j] < 9 && buttons[i][j].isEnabled()){    //base case
            buttons[i][j].setText(String.valueOf(mines[i][j]));
            buttons[i][j].setBackground(Color.WHITE);
            buttons[i][j].setEnabled(false);
         }
      }
      catch(Exception e){                                   //to catch array out of bounds exception
         
      }
   }  
   
  /*******************************************************************************************
   *  The actionPerformed method is called whenever a button is clicked by the user. 
   *  The method uses ActionEvent to detect whenever a button is pressed and reacts as needed
   *  If a blank is clicked, openUpBlanks will be called alongside checkIfWon. If a MINE is
   *  clicked, the endMessage will be shown, etc... In order to get the flags to function, A
   *  boolean value, which triggers everytime the right mouse button is clicked, is used to
   *  place a flag in the according button.                                                       
   *******************************************************************************************/
   public void actionPerformed (ActionEvent e)
   {
      for (int i = 0 ; i < ROW ; i++)           
         for (int j = 0 ; j < COL ; j++)
         {
            if(e.getSource() == buttons[i][j])  //to get which button is pressed
            {
               if (flagProc){                   //flag trigger
                  if(mines[i][j] != 10){        
                     if (flagCounter < NUM_FLAGS){    
                        flagCounter++;
                        buttons[i][j].setIcon(flag);
                        mines[i][j] = 10;      //10 (unattainble with danger) is the flag value
                     }
                  }
                  else{
                     flagCounter--;           //if rightclick is pressed but flag is already there
                     smallCheck(i,j);
                     buttons[i][j].setBackground(null);     //removes flag
                     buttons[i][j].setIcon(null);
                  }
                  flagProc = false;
               }
               
               else if (mines[i][j] == MINE)    //if button is a mine, game is lost
               {
                  buttons[i][j].setIcon(bomb);
                  endMessage(LOST);
               }
               else if (mines[i][j] > 0 && buttons[i][j].isEnabled() && mines[i][j] != 10)   //if button is a number
               {
                  buttons[i][j].setBackground(Color.WHITE);
                  buttons[i][j].setText(String.valueOf(mines[i][j]));
                  buttons[i][j].setEnabled(false);
                  checkIfWon();
               }
               
               else if (mines[i][j] == 0){      //if button is blank
                  openUpBlanks(i,j);
                  checkIfWon();
               }
            }
         }  
      checkIfWon(); //To prevent bugs (if actionevent fails)
   } 
 
}   // END OF THE minesweeper CLASS
