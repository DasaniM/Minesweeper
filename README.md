# Minesweeper
Minesweeper is a game in which the objective of the game is to find the places of a given number of hidden bombs in a close tiled grid. With each move made numbers are revealed indicating the number of bombs surrounding the grid. If the player clicks on a grid with a bomb the game ends.

/**
 *The following class consists of various methods that help combing the bomb class and the board class together and are used in attaing the final output.
 */
class Minesweeper{
    /**
     * This method takes two numbers and find a random integer between or equal to them.
     * @param low:the random integer that is returned cannot be lower than thist integer
     * @param high:the random integer returned cannot be higher than this integer
     * @return int:a random integer between high and low is returned
     */
      public static int randomInt(int low,int high){
          return (int)(Math.random()*((high+1)-low))+low;
    }
    /**
     * This method takes an int specifying the number of bombs it requires and generates a random array of bombs.
     * @param side: the number of bombs to be generated is equal to the length of the side of the grid. This integer specifies how many bombs are to be created
     * @return Bomb[]:This is the random array of bombs that is returned
     */
     public static Bomb[] generate(int side){
         Bomb[]blast = new Bomb[side];
         for(int i = 0;i<side;i++){
             int setX = randomInt(0,(side-1));
             int setY = randomInt(0,(side-1));
             blast[i] = new Bomb(setX,setY);
         }
         return blast;
    }
    /**
     * This method takes an array of bombs and the first input of the player and checks whether there are any two bombs are the same
     * or equal to the first entry that is given by the player.If they are it replaces them with another set of bombs.
     * The aim of this method is to ensure there are no two of the same bombs and to ensure that the player does not loose on their first play.
     * @param blast: This is the Bomb[]array that is checked
     * @param x: This is the row number that is given by the player
     * @param y:This is the column number given by the player
     * @return Bomb[]:This is the new checked and revised array that is returned by the method
     */
     public static Bomb[] checkArray(Bomb[]blast,int x,int y){
        Bomb b = new Bomb (x,y);
         for(int i = 0;i<(blast.length);i++){
             for(int j = 1;j<(blast.length-1);j++){
                 if(blast[i].checkBomb(b)== true){
                     int x1 = randomInt(0,blast.length-1);
                     int y1 = randomInt(0,blast.length-1);
                     blast[i] = new Bomb(x1,y1);
                 }
                 if(blast[i].checkBomb(blast[j])==true){
                     int x2 = randomInt(0,blast.length-1);
                     int y2 = randomInt(0,blast.length-1);
                     blast[j] = new Bomb(x2,y2);
                 }
             }
        }
        return blast;
    }
    /**
     * This method takes an array of bombs and the co-ordinates of the players input and returns a boolean value as to whether the co-ordinates are 
     * equal to that of any of the pre-located bombs.
     * @param blast: This is the array of bombs that is checked
     * @param x:This is the row number that is given by the player
     * @param y:This is the column number that is given by the player
     * @return boolean: This boolean value tells us whether two bombs are the same or are equal to the first co-ordinate of the player
     */
    public static boolean checkBomb(Bomb[]blast,int x,int y){
        for(int i = 0;i<blast.length;i++){
            if(blast[i].x == x && blast[i].y == y){
                return true;
            }
        }
        return false;
    }
    /**
     * This method takes an array of integers and makes a histogram out of the co-ordinates of the bomb
     * @param x:This is an array of either x or y co-ordinates
     * @return int[]:This is the returned and sorted out x co-ordinates 
     */
    public static int[] numberHist(int[]x){
        int[] hist = new int[x.length+1];
        for(int i = 0;i<hist.length;i++){
            hist[i] = 0;
        }
        for (int j = 0;j<x.length;j++){
            int val = x[j];
            hist[val]++;
        }
        return hist;
    }
    /**
     * This method takes the required size of grid as chosen by the player and the first co-ordinates given by the player.
     * It uses the method generate to generate an array of bombs and uses the checkArray method to check them.
     * Then it reduces the number of bombs it is possible to have in a certain row or column to ensure that all the bombs are not clamped up in one area
     * @param side: This is the side of the grid as chosen by the player
     * @param x: This is the first row number given by the player
     * @param y: This is the first column number as chosen by the player
     * @return Bomb[]: This is the final array of bombs that is returned by the method and is used in the playing of the game
     */
     public static Bomb[] finalArray(int side,int x,int y){
        Bomb[] big = generate(side);
        big = checkArray(big,x,y);
        int[]xNo = new int[side];
        int[]yNo = new int[side];
        for(int i = 0;i<side;i++){
            xNo[i] = big[i].x;
            yNo[i] = big[i].y;
        }
        int[]histX = numberHist(xNo);
        int[]histY = numberHist(yNo);
        for(int j = 0;j<side;j++){
            int limit = (side/4)+1;
            int check1 = 0;
            int check2 = 0;
            int index1 = 0;
            int index2 = 0;
            if(histX[j]>limit){
                while(check1<(histX[j]-limit)){
                    if(big[index1].x==j){
                        if(j<side-2){
                            int x1 = j+1;
                            int y1 = big[index1].y;
                            big[index1] = new Bomb(x1,y1);
                        }
                        else{
                            int x2 = j-1;
                            int y2 = big[index1].y;
                            big[index1] = new Bomb(x2,y2);
                        }
                        check1++;
                    }
                    index1++;
                }
            }
            if(histY[j]>limit){
                while(check2<=(histY[j]-limit)){
                    if(big[index2].y==j){
                        if(j<side-2){
                           int x3 = big[index2].x;
                           int y3 = j+1;
                           big[index2] = new Bomb(x3,y3);
                        }
                        else{
                            int x4 = big[index2].x;
                            int y4 = j-1;
                            big[index2] = new Bomb(x4,y4);
                        }
                        check2++;
                    }
                    index2++;
                }
            }
        }
        big = checkArray(big,x,y);
        return big;
    }
    /**
     * This method takes a board and certain co-ordinates and checks the number of bombs there are surrounding it,and assigns it that value. 
     * @param Board: This is a board that has marked positions of bombs on it with this symbol'*'
     * @param x: This is a certain row number
     * @param y:This is a certain column number
     * @return int:This is the returned value of the number of bombs that surround a certain co-ordinate value
     */
    public static int addSurround(Board board,int x,int y){
        int total = 0;
        if(x>0&&x<board.board.length-1&&y>0&&y<board.board.length-1){
          if(board.board[x+1][y] == '*'){
            total++;
          }
          if(board.board[x-1][y] == '*'){
             total++;  
          }          
          if(board.board[x][y+1] == '*'){
            total++;
          }
          if(board.board[x][y-1] == '*'){
            total++;
          }
          if(board.board[x+1][y+1] == '*'){
              total++;
          }
          if(board.board[x+1][y-1] == '*'){
              total++;
          }
          if(board.board[x-1][y+1] == '*'){
              total++;
          }
          if(board.board[x-1][y-1] == '*'){
              total++;
          }
        }
        else if(x==0&&y>0&&y<board.board.length-1){
          if(board.board[x+1][y] == '*'){
            total++;
          }
          if(board.board[x][y+1] == '*'){
            total++;
          }
          if(board.board[x][y-1] == '*'){
            total++;
          }
          if(board.board[x+1][y+1] == '*'){
              total++;
          }
          if(board.board[x+1][y-1] == '*'){
              total++;
          }
        }
        else if(x>0&&x<board.board.length-1&&y==0){
          if(board.board[x+1][y] == '*'){
            total++;
          }
          if(board.board[x-1][y] == '*'){
              total++;
          }
          if(board.board[x][y+1] == '*'){
            total++;
          }
          if(board.board[x+1][y+1] == '*'){
              total++;
          }
          if(board.board[x-1][y+1] == '*'){
              total++;
          }
        }
        else if(x == board.board.length-1&&y>0&&y<board.board.length-1){
          if(board.board[x-1][y] == '*'){
              total++;
          }
          if(board.board[x][y+1] == '*'){
            total++;
          }
          if(board.board[x][y-1] == '*'){
            total++;
          }
          if(board.board[x-1][y+1] == '*'){
              total++;
          }
          if(board.board[x-1][y-1] == '*'){
              total++;
          }
        }
        else if(x>0&&x<board.board.length-1&&y==board.board.length-1){
          if(board.board[x+1][y] == '*'){
            total++;
          }
          if(board.board[x-1][y] == '*'){
              total++;
          }
          if(board.board[x][y-1] == '*'){
            total++;
          }
          if(board.board[x+1][y-1] == '*'){
              total++;
          }
          if(board.board[x-1][y-1] == '*'){
              total++;
          }
        }
        else if(x==0&&y==0){
          if(board.board[x+1][y] == '*'){
            total++;
          }
          if(board.board[x][y+1] == '*'){
            total++;
          }
          if(board.board[x+1][y+1] == '*'){
              total++;
          }
        }
        else if(x==board.board.length-1&&y==board.board.length-1){
          if(board.board[x-1][y] == '*'){
              total++;
          }
          if(board.board[x][y-1] == '*'){
            total++;
          }
          if(board.board[x-1][y-1] == '*'){
              total++;
          }
        }
         else if(x == board.board.length-1&&y == 0){
            if(board.board[x-1][y+1] == '*'){
                total++;
            }
            if(board.board[x][y+1] == '*'){
                total++;
            }
            if(board.board[x-1][y] == '*'){
                total++;
            }
        }
        else if(x == 0&&y == board.board.length-1){
            if(board.board[x+1][y-1] == '*'){
                total++;
            }
            if(board.board[x+1][y] == '*'){
                total++;
            }
            if(board.board[x][y-1] == '*'){
                total++;
            }
        }
        else{
            total = total;
        }
        return total;
    }
    /**
     * The following method uses the addSurround method to create a board consisting of the values hidden to the player and bomb locations.
     * It requires an array of bombs to be invoked.
     * @param mines: This is the final array of mines that will be used in creating a Board with all the essential numbers and bombs
     * @return Board:This is the returned Board that contains all the actual values
     */
    public static Board boardVals(Bomb[]mines){
        char[][]vals = new char[mines.length][mines.length];
        Board board = new Board(vals);
        for(int i = 0; i<mines.length;i++){
            for(int j = 0; j<mines.length;j++){
                vals[i][j] = '0';
            }
        }
        for(int i = 0;i<mines.length;i++){
            int x = mines[i].x;
            int y = mines[i].y;
            vals[x][y] = '*';
        }
        for(int i = 0;i<mines.length;i++){
            for(int j = 0;j<mines.length;j++){
                int set = addSurround(board,i,j);
                char put = ' ';
                if(board.board[i][j]=='*'){
                    put = '*';
                }
                else{
                    put = (char)(set+48);
                }
                board.board[i][j]= put;
            }
        }
        return board;
    }
}

