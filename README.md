# Assignment2
#### I am trying to draw an glitch art image, a human eye image with color shift quads to form a pupil. I want nosie on whole image to make a coarse feeling. And horiztonl shift to make it looks more broken.
## Result Image
![Result Image](/img/Resutl_Final.png)

Link: https://editor.p5js.org/YidiLiu1216/full/1GLihYb3z
## Original Instructions
```
 \\Target: draw an eyeball with glitch art pupil and glitch effect
 \\1. Draw Small Circle as iris outside circle 
 \\2. Draw lines to form a circle as pupil inside
 \\3. Draw dark circle to make center of pupil darker
 \\4. Draw highlights of pupil with circle
 \\5. Calculate random size quads around center as basic shape of pupil
 \\6. Caluculate random color shift value
 \\7. Draw red, green, blue channel quads based on color shift value
 \\8. Draw outside circle of the eyeball
 \\9. Apply random noise on the whole image
 \\10.Save the Eye Image as a eye
 \\11. Apply horiztonl shift effect by random copy and paste bar of image on canvas
\\12. Apply Scanline effect by draw darken lines every few pixels
```
## Full Code
```
let eyeGraphic;

function setup() {
  createCanvas(800, 800);
  angleMode(RADIANS);
  pixelDensity(1);
  background(0);
  noLoop();
  
  eyeGraphic = createGraphics(width, height);
  eyeGraphic.angleMode(RADIANS);
  eyeGraphic.pixelDensity(1);
  
}

function draw() {
  drawEyeImage();
  image(eyeGraphic, 0, 0);
  HorizontalGlitch(eyeGraphic);
  drawScanlines();

}
function HorizontalGlitch(g){
  let minStripNum=10;
  let maxStripNum=20;
  let maxStripHeight=23;
  let horizontalShiftValue=80;
  let dropRate=0.15;
  let extraShiftRate=0.2;
  
  
  let stripnum=random(minStripNum,maxStripNum);
  for(let i=0;i<stripnum;i++){
    let y =random(g.height);
    let h = random(2,maxStripHeight);
    let xShift = random(-horizontalShiftValue,horizontalShiftValue);
    
    if(random()<extraShiftRate){xShift*=3;}
    
    //Drop random Black Bar
    if(random()<dropRate){
      blendMode(BLEND);
       noStroke();
      fill(0,255);
      rect(0,int(y),int(width),int(h));
    }else{//Horizontal Shift
    copy(g,0,int(y),int(g.width),int(h),int(xShift),int(y),int(width),int(h));
    }
    
  }
    

  
}
function drawScanlines() {
  noStroke();
  fill(0, 90);
  for (let y = 0; y < height; y += int(random(3, 6))) {
    rect(0, y, width, 1);
  }
}

function drawEyeImage(){
    //Overall setting
  eyeGraphic.translate(eyeGraphic.width/2,eyeGraphic.height/2);//Move the coordinate to canvas center

  
  //Setting about iris
  let irisInnerR=30; let irisOutR=80;
  let irisDarkenR=65;
  
  //Setting about pupil
  let lineCount=60; 
  let minInsideR=80;
  let maxInsideR=100;
  let minOutsideR=200;
  let maxOutsideR=250;
  
  //PAINT IRIS
  //Iris lines to form a circle
  eyeGraphic.noFill();
  eyeGraphic.stroke(180);
  eyeGraphic.strokeWeight(1);
  drawLineCircle(eyeGraphic,120,irisInnerR,irisOutR);
  //Iris outcircle
  eyeGraphic.noFill();
  eyeGraphic.stroke(225);
  eyeGraphic.strokeWeight(2);
  eyeGraphic.circle(0,0,irisOutR*2); 
  //Iris  darken circle
  eyeGraphic.strokeWeight(0);
  eyeGraphic.fill(0, 150);
  eyeGraphic.circle(0, 0, irisDarkenR*2);
  //Iris high light circle
  eyeGraphic.fill(255, 200);
  eyeGraphic.circle(-irisOutR *2 * 0.25, -irisOutR* 2 * 0.25, 160 * 0.25);
  eyeGraphic.fill(255, 120);
  eyeGraphic.circle(-irisOutR *2 * 0.12, -irisOutR *2 * 0.35, 160 * 0.12);
  
  //PAINT PUPIL
  for(let i = 0; i < lineCount; i++){
    //random pupil quads length and width
    let insideR=random(minInsideR,maxInsideR);
    let outsideR= random(minOutsideR,maxOutsideR);
    
    let angle = TWO_PI*i/lineCount;
   
    let maxquadWidth=1;
    let minquadWidth=0.2;
    let randomWidth=(random(minquadWidth,maxquadWidth)*TWO_PI)/lineCount;
    //draw color shift quads
    colorShiftquad(eyeGraphic,angle,randomWidth,insideR,outsideR,lineCount);
    
  }
 
  //Draw out side circle as border 
  eyeGraphic.noFill();
  eyeGraphic.stroke(100);
  eyeGraphic.strokeWeight(8);
  eyeGraphic.circle(0,0,540);
  
  //Apply noise on whole image
  drawRandomNoise(eyeGraphic);

}
//This fucntion draw LinesAround a circle
function drawLineCircle(g,lineCount,insideR,outsideR){
  for(let i = 0; i < lineCount; i++){
    let angle = TWO_PI*i/lineCount;
    let insideX= cos(angle)*insideR;
    let insideY= sin(angle)*insideR;
    let outsideX= cos(angle)*outsideR;
    let outsideY = sin(angle)*outsideR;
    
    g.line(insideX,insideY,outsideX,outsideY);
  }
  
}

//This function draw Quad with given angle position, width and length
function drawRectangle(g,startAngle,quadWidth,insideR,outsideR,lineCount){

  
  let firstAngle = startAngle;
  let secondAngle = startAngle+quadWidth;

  let point_1X=cos(firstAngle)*insideR; let point_1Y=sin(firstAngle)*insideR; 
  let point_2X = cos(firstAngle)*outsideR; let point_2Y=sin(firstAngle)*outsideR;
  let point_3X=cos(secondAngle)*insideR; let point_3Y=sin(secondAngle)*insideR;
  let point_4X=cos(secondAngle)*outsideR; let point_4Y=sin(secondAngle)*outsideR;
  
  g.quad(point_1X,point_1Y,point_2X,point_2Y,point_4X,point_4Y,point_3X,point_3Y);

}

//This founction apply random noise on image
function drawRandomNoise(g){
  g.loadPixels();
  let alphaMix = 0.2;
  
  for (let x = 0; x < g.width; x++) {
    for (let y = 0; y < g.height; y++) {
      
      //calculate the index position in the pixel list
      let index = (x + y * g.width) * 4;
      let value = random(255);
      
      g.pixels[index] = lerp(g.pixels[index],value, alphaMix);
      g.pixels[index+1] = lerp(g.pixels[index+1],value, alphaMix);
      g.pixels[index+2] = lerp(g.pixels[index+2],value, alphaMix);

    }
  }  
  g.updatePixels();
}

//This function apply random color shift on given quad
function colorShiftquad(g,startAngle,quadWidth,insideR,outsideR,lineCount){
  g.blendMode(ADD);
  let off = random(30);
  let startAngleoff=random(0.1);
  g.noStroke();
  g.fill(255,0,0,250);
  drawRectangle(g,startAngle+startAngleoff,quadWidth,insideR+off,outsideR+off,lineCount);
  g.fill(0,255,0,250);
  drawRectangle(g,startAngle,quadWidth,insideR,outsideR,lineCount);
  g.fill(0,0,255,250);
  drawRectangle(g,startAngle-startAngleoff,quadWidth,insideR-off,outsideR-off,lineCount);
  g.blendMode(BLEND);
}

```
