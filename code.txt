setSize(720,480); //sets the screen size to 480p 16:9 aspect ratio
    /*Credits at the bottom of the code*/


//Global Vars
var cookiesInBank = 0; // Amt of cookies
var cookiesPerClick = 1; // Cookies per click (CPC)
var cookiesPerSecond = 0; // Cookies per Second (CPS)
var counter;
var buildingPrices = [10,55,100,225,750,1500,4000,10000,23000]; // Prices of the buildings
var buildingPriceDisplay = [ , , , , , , , , , ];//Stores the text vars for prices.
var buildings = [ "Cursor", "Grandma", "Farm", "Mine", "Factory", "Bank", "Temple", "Rocket", "Portal"]; // All of the building names
var cookiesPerBuilding = [1, 1, 5, 15, 35, 60, 100, 140, 200]; // The amunt of cookies each building adds
var chips = []; // The chips variable is to store all of the chips so they can be randomized later
var ownedLabel= [ , , , , , , , , ];
var owned=[0,0,0,0,0,0,0,0,0];
var cps;
var cpc;
var lines=[]; 
var upgradePrices = [1000,2000,5000,10000,15000,20000,50000,100000,200000]; 
var upgradePriceDisplay = [ , , , , , , , , , ]; 
var skipCustomization = false;

function makeText(xPos,yPos,text,font,color,alpha){ //Parameters are self explanatory. (alpha means opacity)
    var text = new Text(text,font);
    text.setPosition(xPos,yPos);
    text.setColor(color);
    text.setOpacity(alpha);
    add(text);
    return text; //If the text needs to be stored in an array or other var this will do that
}

function start(){ //Draw background and game, main handler for everything
    
    var bgColors = getBackgroundColors();
    if(bgColors == "skip"){ 
        lines=drawBackground("default");
        drawGame(true);
    } else {
        lines=drawBackground(bgColors);
        drawGame(false);
    }
    
    var bgMusic=new Audio("https://codehs.com/uploads/daa5905ca9347beaac29a610fff3f13a"); 
    /*Music is "After Hours" by Heaven Pierce Her, \
    listen to it at https://heavenpierceher.bandcamp.com/track/after-hours-3*/
    bgMusic.play();
    bgMusic.loop=true; //Loops the music if true, opposite if false
    
    keyUpMethod(keyUp); // ANY KEY PRESSING FUCNTIONS GO HERE!
    keyDownMethod(keyDown);
    setTimer(autoBake, 1000);
}

function getBackgroundColors(){ //Gets BG colors from user to use 
    var color1=readLine("What is the first color you want the background to be? (Type default to get default colors)");
    if (color1.toLowerCase() == "skip"){
        return "skip";
    } else if(color1.toLowerCase() == "default"){
        return color1.toLowerCase();
    }else{
        var color2 = readLine("What is the second color you want the background to be? ");
    
        return [color1, color2];
    }
    
}

function drawBackground(colors){ //draws background
    var temp=[]; //Temporary, dunno why we even added it
    if (colors == "default"){
        for(var i = 0; i < 60; i++){
            var line = new Rectangle(12,getHeight());
            line.setPosition(i*12,0);
            if(i % 2 == 0){ //If else to alternate between both colors.
                line.setColor("lightblue");
            } else {line.setColor("blue");}
            add(line);
            
            temp.push(line);
        }    
    } else { //executes if user inputs actual colors to use
        for(var i = 0; i < 60; i++){
            var line = new Rectangle(12, getHeight());
            line.setPosition(i*12, 0);
            if(i % 2 == 0){ //If else to alternate between both colors.
                line.setColor(colors[0]);
            }else{line.setColor(colors[1]);}
            add(line);
            
            temp.push(line);
        }    
    }
    
    
    var dimmer=new Rectangle(getWidth(),getHeight());
    dimmer.setOpacity(0.3); //Makes the background a bit darker so everything else is easier to see
    add(dimmer);
    
    return temp; //Should go back to the lines global var.
}

function updateBackground(colors){ //updates the background
    if(colors=="default"){
        for(var i=0;i<60;i++){
            if(i%2==0){
                lines[i].setColor("lightblue");
            }else{lines[i].setColor("blue");}
        }    
    }else{
        for(var i=0;i<60;i++){
            if(i%2==0){
                lines[i].setColor(colors[0]);
            }else{lines[i].setColor(colors[1]);}
        }    
    }
}

function drawGame(skipCustomizations){ //Calls on everything to draw the game
    var upSize = 30;
    var buildingSprites=[
        "https://codehs.com/uploads/fbf37f0da7e1015b1fed560512a59081",// Cursor
        "https://codehs.com/uploads/f836fdec1f0f1005b51293245210a61e",// Grandma
        "https://codehs.com/uploads/e3c8a0815d38188361423239be672585",// Farm
        "https://codehs.com/uploads/72584dcfcc541df9db73d752ff8384cf",// Mine
        "https://codehs.com/uploads/7a8a1f51c9371cca5e0ef72d926098e9",// Factory
        "https://codehs.com/uploads/06bca92ec2e05fd4f4aab5d103c33d00",// Bank
        "https://codehs.com/uploads/25fd01e09b8c338dbea693fc586d5012",// Temple
        "https://codehs.com/uploads/2627a0e31ee9901b56b5f5bb3beb7a33",// Rocket
        "https://codehs.com/uploads/d179f624414fa96a2cd68ecfb079abd0"]// Portal
    
    if(skipCustomizations){ //if you just have the boolean name it auto checks for if its true or false, the == true is redundant. // Oh ok. Still makes more sense with the true.
        var fontBank = "Times New Roman"
    }else{
        var fontBank = getFont();
    }
    makeCookie();
    addUnneccessary(1000, 1000, "Sienna");
    makeCountBar(fontBank);
    drawBuildings(upSize,buildingSprites);
    drawUpgrades(upSize);
    settings();
}

function getFont(){ //Gets user font
    if (skipCustomization == true){
        return "Times New Roman"
    }
    var mono = readLine("What font should your cookies be displayed in? ");
    return mono;
}

function settings(){ //Displays keybinds and user stats
    //==========================
    //  KEYBINDS
    //==========================
    
    makeText(555,55,"Keybinds:","23pt Times New Roman","white",1); //Header
  
    makeText(555,80,"Space: Bake a Cookie","10pt Arial","white",1);
    
    makeText(555,100,"1 - 9: Buy Buildings","10pt Arial","white",1);

    makeText(555,120,"Q - O: Buy Upgrades","10pt Arial","white",1);

    makeText(555,140,"K: New Cookie Chips","10pt Arial","white",1);

    makeText(555,160,"B: Change Background","10pt Arial","white",1);
    
    makeText(555,180,"C: Change Counter Font","10pt Arial","white",1);
    
    //==========================
    //  STATS
    //==========================

    makeText(555,220,"Stats:","23pt Times New Roman","white",1); //Header
    
    //Produces statistics for each building owned
    for(var i=0;i<buildings.length;i++){
        ownedLabel[i]=makeText(555,240+(20*i),buildings[i] + " Owned: " + owned[i],"10pt Arial","white",1);
    }
    cps=makeText(555,430,"CPS: " + cookiesPerSecond,"10pt Arial","white",1);

    cpc=makeText(555,450,"CPC: " + cookiesPerClick,"10pt Arial","white",1);
    
}

function makeCookie(){ //draws the cookie
    var cookie = new Circle(100);
    cookie.setPosition(getWidth()/2, getHeight()/2);
    cookie.setColor("#aa7733");
    add(cookie);
    
    randomizeChips();
}

function randomizeChips() { //draws the cookie chips
    for (var i = 0; i < chips.length; i++) {
        remove(chips[i]);
    }
    chips = [];
    for (var i = 0; i < 8; i++) {
        var randomX = Randomizer.nextInt(getWidth() / 2 - 60, getWidth() / 2 + 60);
        var randomY = Randomizer.nextInt(getHeight() / 2 - 60, getHeight() / 2 + 60);
        var chip = new Circle(10);
        chip.setPosition(randomX, randomY);
        chip.setColor("#553311");
        add(chip);
        chips.push(chip);
    }
}

function makeCountBar(font){ //Draws the counter that displays your current cookies
    counter = new Text(cookiesInBank, "30pt " + font);
    counter.setColor("White");
    counter.setPosition(getWidth()/2-counter.getWidth()/2,63);
    add(counter);
}

function addUnneccessary(width, height, color){ //One function to store nonessential graphics
    
    var upgradesBackground = new Rectangle(200,getHeight());
    upgradesBackground.setPosition(0,0);
    upgradesBackground.setColor("grey");
    add(upgradesBackground);
    
    var cookieCountBox=new Rectangle(320,50);
    cookieCountBox.setPosition(200,25);
    cookieCountBox.setOpacity(0.7);
    add(cookieCountBox);
    
    var stats = new Rectangle(150, 445);
    stats.setPosition(550, 20);
    stats.setColor("black");
    stats.setOpacity(0.7); 
    add(stats);
    
    var border1 = new Rectangle(10, width);
    border1.setPosition(200, getHeight()/2-300);
    border1.setColor(color);
    add(border1);
    
    var border2 = new Rectangle(330, 10);
    border2.setPosition(200, 100);
    border2.setColor(color);
    add(border2);
    
    var border3 = new Rectangle(10, width);
    border3.setPosition(520, getHeight()/2-300);
    border3.setColor(color);
    add(border3);
    
}

function keyUp(e){ //Handles clicking the cookie and customization buttons (and the debug cash)
    if(e.key == " "){ //Click space
        cookiesInBank += cookiesPerClick;
        counter.setText(cookiesInBank);
        counter.setPosition(getWidth()/2-counter.getWidth()/2,63);
    } else if (e.key == "k"){
        randomizeChips();
    } else if (e.key == "b"){
        stopTimer(autoBake); //stops autobake to prevent stacking cps
        updateBackground(getBackgroundColors());
        setTimer(autoBake, 1000);
    } else if (e.key == "c"){
        remove(counter);
        makeCountBar(getFont());
    }else if(e.key == "l"){
        
        cookiesInBank += 9999999999999;
        
        println("You're rich! Use this cash to debug.")
    }
}

function keyDown(e){ //Handles clicking for buying
    var upgradeKey=["q","w","e","r","t","y","u","i","o"];
    if(e.key >= 1 && e.key <= 9){
        if(cookiesInBank >= buildingPrices[e.key - 1]){
            cookiesInBank-=buildingPrices[e.key - 1];
            if(e.key==1){
                cookiesPerClick++;
            }else{
                cookiesPerSecond += cookiesPerBuilding[e.key - 1];
            }
            buildingPrices[e.key - 1] = Math.round(buildingPrices[e.key - 1]*1.2); //Prevents price from being a decimal
            counter.setText(cookiesInBank);
            counter.setPosition(getWidth()/2-counter.getWidth()/2,63);
            buildingPriceDisplay[e.key - 1].setText(buildingPrices[e.key - 1]);
            buildingPriceDisplay[e.key - 1].setPosition(200-buildingPriceDisplay[e.key - 1].getWidth()-5,buildingPriceDisplay[e.key-1].getY());
            buyPopUp(e.key-1,0);
            owned[e.key-1]++;
            ownedLabel[e.key-1].setText(buildings[e.key-1] + " Owned: " + owned[e.key-1]);
            cps.setText("CPS: " + cookiesPerSecond);
            cpc.setText("CPC: " + cookiesPerClick);
        }
    } else if (upgradeKey.indexOf(e.key)!=-1){
        var chosenUpgrade = upgradeKey.indexOf(e.key); //Way too long to copy paste
        if(cookiesInBank >= upgradePrices[chosenUpgrade]){
            cookiesInBank -= upgradePrices[chosenUpgrade];
            upgradePrices[chosenUpgrade]=Math.round(upgradePrices[chosenUpgrade]*=1.5);
            upgradePriceDisplay[chosenUpgrade].setText(upgradePrices[chosenUpgrade]);
            if(chosenUpgrade==0){
                cookiesPerClick*=2; //Cursors are 10x more OP now yay!!!!
            }else{cookiesPerBuilding[chosenUpgrade]*=2;}
            buyPopUp(chosenUpgrade,1)
            updateCps();
            cps.setText("CPS: " + cookiesPerSecond);
            cpc.setText("CPC: " + cookiesPerClick);
            
            
            
        }
    }
}

function updateCps(){ //Recalculates CPS after buying upgrades
    cookiesPerSecond=0;
    for(var i=1;i<buildings.length;i++){
        cookiesPerSecond+=owned[i]*cookiesPerBuilding[i];
    }
}
    
function drawBuildings(size,sprites){ // Draws building panels

    var upgradesLabel=new Text("SHOP", "27pt Times New Roman");
    makeText(100-(upgradesLabel.getWidth()/2),40,"SHOP","27pt Times New Roman","white",1); //Header
    
    var initBorder = new Rectangle(200, 2);
        initBorder.setPosition(0, 53);
        initBorder.setColor("Sienna");
        add(initBorder);
    
    for(var i = 0; i < buildingPriceDisplay.length; i++){
        var yOffset = 30*i //Change the integer value on this line to edit the spacing between each price

        makeText(35,77+yOffset,buildings[i],"20pt Arial","black",0.5);
        
        var buildingSprite = new WebImage(sprites[i]);
        buildingSprite.setSize(size,size);
        buildingSprite.setPosition(0,55+yOffset);
        add(buildingSprite);
 
        buildingPriceDisplay[i]=new Text(buildingPrices[i],"18pt Arial"); //This has to be defines first so the .getWidth function works properly.
        buildingPriceDisplay[i]=makeText(200-buildingPriceDisplay[i].getWidth()-5,77+yOffset,buildingPrices[i],"18pt Arial","white",1);
        
        var border = new Rectangle(200, 2);
        border.setPosition(0, 83+yOffset);
        border.setColor("Sienna");
        add(border);
    }
}

function autoBake(){ //Handles passive cookie generation
    cookiesInBank += cookiesPerSecond;
    counter.setText(cookiesInBank);
    counter.setPosition(getWidth()/2-counter.getWidth()/2,63);
}

function buyPopUp(selectedBuilding,buyType){ //Creates and destroys the popups for buying
    var box = new Rectangle(300, 50);
    box.setPosition(215,350);
    box.setColor("black");
    add(box);
    
    var bought=["Bought a ","Upgraded "];
    
    var popUp = new Text(bought[buyType] + buildings[selectedBuilding], "20pt Arial") //draw this later so its on top (This has to be defines first so the .getWidth function works properly.)
    popUp=makeText(getWidth()/2-popUp.getWidth()/2,385,bought[buyType] + buildings[selectedBuilding],"20pt Arial","white",1);
    
    setTimeout(function(){remove(popUp); remove(box);},3000); //will remove the pop up after 3 seconds
}

function drawUpgrades(size){ //draws upgrade panels
    var upgradeSprites = [
    "https://codehs.com/uploads/6d3c732090ce3b37128844e6264fcf4c" , // Cursor
    "https://codehs.com/uploads/219380db95baf7c001c2ea66bccb8854" , // Grandma
    "https://codehs.com/uploads/e2c2126e442bcfa536b28b6a333f3fda" , // Farm
    "https://codehs.com/uploads/01aa2d416876392f0534772cd917a96d" , // Mine
    "https://codehs.com/uploads/7b3386fef20c8ac7e76e4f41b4e06cb3" , // Factory
    "https://codehs.com/uploads/3ccd9eaddb7ee91d38aa52a6b3dfde79" , // Bank
    "https://codehs.com/uploads/88456ad3d929e8f30bdd25fcc132919d" , // Temple
    "https://codehs.com/uploads/917d6f0cf184ea22be013ad579e97736" , // Rocket
    "https://codehs.com/uploads/ae18de661b257586ad71128f63e922b1"// Portal
    ] 
    var sizeIncrease=19;
    for(var i = 0; i < buildings.length; i++){
        if(i % 3 == 0){
            var yOffset=50 * (i/3); //Every 3 icons, they go down by 50
        }
        var xOffset = 60 * (i%3);
        var upgradeSprite = new WebImage(upgradeSprites[i]);
        upgradeSprite.setSize(size+sizeIncrease,size+sizeIncrease);
        upgradeSprite.setPosition(5, 322);
        upgradeSprite.move(xOffset,yOffset);
        add(upgradeSprite);

       upgradePriceDisplay[i] = makeText(15+xOffset,374+yOffset,upgradePrices[i],"10pt Arial","white",1);
    } 
    
}

/*******************************************************************************
Upgrade Sprites
Cursor upgrade: https://codehs.com/uploads/6d3c732090ce3b37128844e6264fcf4c
Grandma upgrade: https://codehs.com/uploads/219380db95baf7c001c2ea66bccb8854
Farm upgrade: https://codehs.com/uploads/e2c2126e442bcfa536b28b6a333f3fda
Mine upgrade: https://codehs.com/uploads/01aa2d416876392f0534772cd917a96d
Factory upgrade: https://codehs.com/uploads/7b3386fef20c8ac7e76e4f41b4e06cb3
Bank upgrade: https://codehs.com/uploads/3ccd9eaddb7ee91d38aa52a6b3dfde79
Temple upgrade: https://codehs.com/uploads/88456ad3d929e8f30bdd25fcc132919d
Rocket upgrade: https://codehs.com/uploads/917d6f0cf184ea22be013ad579e97736
Portal upgrade: https://codehs.com/uploads/ae18de661b257586ad71128f63e922b1

Building Sprites
Building upgrades sprite sheet: https://codehs.com/uploads/fd2ff13447c5c12ee4d1448f3c65629f
Cursor Building: https://codehs.com/uploads/fbf37f0da7e1015b1fed560512a59081
Grandma Building: https://codehs.com/uploads/f836fdec1f0f1005b51293245210a61e
Farm building: https://codehs.com/uploads/905e5b5b9f7cad56fa4a228f9a34aea7
Mine Building: https://codehs.com/uploads/86bdc8433f21dd7f3fb380455ca50174
Factory Building: https://codehs.com/uploads/0af1da593d9d6bece22d47c7e55bee1d
Bank Building: https://codehs.com/uploads/5929a40fec6701680c26cc7c5f038e39
Temple Building: https://codehs.com/uploads/3e9cc4e787089a4d07a117eecdb6e973
Rocket Building: https://codehs.com/uploads/d97eb4cca6f0ac40d0a58f73b0cd1a9a
Portal Building: https://codehs.com/uploads/61893545dd81c05f5ca6e27eb061f9a1


All sprites from Cookie Clicker by Orteil. Orteil's website: orteil.dashnet.org Cookie Clicker: orteil.dashnet.org/cookieclicker
Music "After Hours" by Heaven Pierce Her. Listen to it at https://heavenpierceher.bandcamp.com/track/after-hours-3
*******************************************************************************/