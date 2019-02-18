mgraphics.init();
mgraphics.relative_coords = 1; // allows for 0-1 coords
mgraphics.autofill = 0;

var number_of_rings = 6;
var max_size = 0.9;

var mouse = new Point();

var dragging = false;
var hiddenText = false;

var rings = [];

var active_colors = [
  new Color(0.7 + 0.3,0.4 + 0.3,0.8 + 0.3,1.0),
  new Color(0.9 + 0.3,0.3 + 0.3,0.3 + 0.3,1.0),
  new Color(0.9 + 0.3,0.5 + 0.3,0.1 + 0.3,1.0),
  new Color(0.9 + 0.3,0.8 + 0.3,0.1 + 0.3,1.0),
  new Color(0.4 + 0.3,0.7 + 0.3,0.2 + 0.3,1.0),
  new Color(0.3 + 0.3,0.6 + 0.3,0.9 + 0.3,1.0)
];

var inactive_colors = [
  new Color(0.7,0.42,0.89,1),
  new Color(0.93,0.36,0.34,1),
  new Color(0.95,0.56,0.10,1),
  new Color(0.96,0.83,0.16,1),
  new Color(0.44,0.75,0.25,1),
  new Color(0.32,0.65,0.98,1)
];

onresize();

function bang(){
    mgraphics.redraw();
}

function reset(){
  rings = [];
  for(var i = 0; i < number_of_rings; i++){
    rings.push(new Ring((max_size/number_of_rings)*(i+1),(number_of_rings-1)-i));
    rings[i].updateObjects();
  }
  onresize();
  mgraphics.redraw();
}

function paint(){
  drawAlignmentCompass(0.3);
  for(var i = 0; i < rings.length; i++){
    rings[i].update();
    rings[i].display();
  }

  for(var i = 0; i < rings.length; i++){
    rings[i].drawControl();
    rings[i].drawText();
  }
}

//------------------------------------------------------------------------------
// RING
function Ring(radius, id){
  this.id = id;

  this.textcolor = new Color(0,0,0,1);
  this.ctrl_on = active_colors[id];
  this.ctrl_off = inactive_colors[id];

  this.selected = false;
  this.center = new Point(0.0,0.0);
  this.offset = 0;
  this.theta  = 0;
  this.radius = radius;
  this.type   = 0;

  this.oscfreq  = getObject("oscfreq"+id);
  this.oscamp   = getObject("oscamp"+id);
  this.osctype  = getObject("osctype"+id);
  this.osctheta = getObject("osctheta"+id);

  if(this.oscfreq){
    this.frequency = this.oscfreq.getvalueof();
  }

  this.ctrl_radius = 0.08;
  this.ctrl_position   = new Point();
  this.ctrl_position.x = (this.radius*max_size)*Math.cos(this.theta) + this.center.x;
  this.ctrl_position.y = (this.radius*max_size)*Math.sin(this.theta) + this.center.y;

  this.display = function(){
    // post("hold on here is this always being called? \n");
    with(mgraphics){
      set_source_rgba(0,0,0,1.0);
      translate(-this.radius,-this.radius);
      ellipse(0,0,this.radius*2,this.radius*2);

      identity_matrix();

      move_to(this.center.x,this.center.y);
      line_to(this.ctrl_position.x,this.ctrl_position.y);
      close_path();
      stroke();
    }
  }

  this.drawControl = function(){
    with(mgraphics){
      if(this.type == 0){ // sine (circle)
        ellipse(
          this.ctrl_position.x-(this.ctrl_radius / 2),
          this.ctrl_position.y+(this.ctrl_radius / 2),
          this.ctrl_radius,
          this.ctrl_radius
        );
      }else if(this.type == 1){ // triangle
        move_to(this.ctrl_position.x,this.ctrl_position.y - 0.04);
        rel_move_to(this.ctrl_radius/2 + 0.01,0);
        rel_line_to(-this.ctrl_radius/2 - 0.01, this.ctrl_radius);
        rel_line_to(-this.ctrl_radius/2 - 0.01, -this.ctrl_radius);
        close_path();
      }else if(this.type == 2){ // saw TODO: do better
        move_to(this.ctrl_position.x,this.ctrl_position.y);
        rel_move_to(-this.ctrl_radius/2,-this.ctrl_radius/2 + 0.01);
        rel_line_to(0,this.ctrl_radius);
        rel_line_to(this.ctrl_radius,-this.ctrl_radius);
        rel_line_to(0,this.ctrl_radius);
      }else if(this.type == 3){ // square
        rectangle(
          this.ctrl_position.x - this.ctrl_radius/2,
          this.ctrl_position.y + this.ctrl_radius/2,
          this.ctrl_radius,
          this.ctrl_radius
        );
      }
      close_path();
      set_source_rgb(this.ctrl_off.r,this.ctrl_off.g,this.ctrl_off.b); // default control color

      if(this.selected){
        set_source_rgb(this.ctrl_on.r,this.ctrl_on.g,this.ctrl_on.b);
      }

      fill_preserve();

      set_source_rgb(0,0,0);
      stroke();
    }
  }

  this.drawText = function(){
    with(mgraphics){
      if(!hiddenText){
        // post(this.frequency+"\n");
        var formatted_freq = parseFloat(this.frequency).toFixed(2);
        // var formatted_freq = this.frequency;
        set_source_rgb(0,0,0);

        var text_dim = text_measure(formatted_freq+" hz");
        move_to(
          this.ctrl_position.x - (this.ctrl_radius/2 + (text_dim[0]/getWidth())/2) + 0.008,
          this.ctrl_position.y + this.ctrl_radius - (text_dim[1])/getHeight() + 0.01
        );
        show_text(formatted_freq+" hz");
        stroke();

        move_to(
          this.ctrl_position.x - (this.ctrl_radius/2 + ((text_dim[0]/getWidth()) + 0.02) /2),
          this.ctrl_position.y - this.ctrl_radius - ((text_dim[1])/getHeight())+0.01
        );
        show_text("Θ = "+this.theta.toFixed(2));

        if(this.selected){
          this.textcolor = new Color(1,1,1,1);
        }else{
          this.textcolor = new Color(0,0,0,1);
        }

        set_source_rgb(this.textcolor.r,this.textcolor.g,this.textcolor.b);
        stroke();
      }
    }
  }

  this.update = function(){
    this.checkParamsChanged();

    if(this.selected){
      this.offset = Math.atan2(mouse.y - this.center.y,mouse.x - this.center.x);
      this.radius = getDistance(0,0,mouse.x,mouse.y);
    }

    this.ctrl_position.x = this.radius * Math.cos(this.offset) + this.center.x;
    this.ctrl_position.y = this.radius * Math.sin(this.offset) + this.center.y;

    this.theta = Math.atan2(
      this.ctrl_position.y - this.center.y,
      this.ctrl_position.x - this.center.x
    );

    this.updateObjects();
  }

  this.updateObjects = function(){
    this.oscamp.float(this.radius/max_size);
    this.osctheta.float(this.theta);
    this.osctype.set(this.type);
  }

  this.updateUI = function(active){
    if(active){
       this.osctype.activebgcolor(this.ctrl_on.r,this.ctrl_on.g,this.ctrl_on.b,1);
       this.oscamp.activebgcolor(this.ctrl_on.r,this.ctrl_on.g,this.ctrl_on.b,1);
       this.osctheta.activebgcolor(this.ctrl_on.r,this.ctrl_on.g,this.ctrl_on.b,1);
       this.oscamp.textcolor(0,0,0,1);
       this.osctheta.textcolor(0,0,0,1);
       this.osctype.textcolor(0,0,0,1);
    }else{
       this.oscamp.activebgcolor(0.2,0.2,0.2,1);
       this.osctheta.activebgcolor(0.2,0.2,0.2,1);
       this.osctype.activebgcolor(0.2,0.2,0.2,1);
       this.oscamp.textcolor(1,1,1,1);
       this.osctheta.textcolor(1,1,1,1);
       this.osctype.textcolor(1,1,1,1);
    }
  }

  this.checkParamsChanged = function(){
    if(this.radius != this.oscamp.getvalueof()){ //param changed
      this.updateUI(this.selected,"radius");
      this.radius = this.oscamp.getvalueof() * max_size;
    }

    if(this.theta != this.osctheta.getvalueof()){ //param changed
      this.updateUI(this.selected,"theta");
      this.offset = this.osctheta.getvalueof();
    }

    if(this.type != this.osctype.getvalueof()){ //param changed
      this.updateUI(this.selected,"type");
      this.type = this.osctype.getvalueof();
    }

    if(this.frequency != this.oscfreq.getvalueof()){
      this.frequency = this.oscfreq.getvalueof();
    }
  }
}

//------------------------------------------------------------------------------
// EVENT HANDLERS
function ondrag(x,y,button){
  var loc = sketch.screentoworld(x,y);
  mouse.x = loc[0];
  mouse.y = loc[1];

  if(button){
    for(var i = 0; i < rings.length; i++){
      if(!dragging){
        if( mouse.x > (rings[i].ctrl_position.x - (rings[i].ctrl_radius / 2))
          && mouse.x < (rings[i].ctrl_position.x + (rings[i].ctrl_radius / 2))
          && mouse.y > (rings[i].ctrl_position.y - (rings[i].ctrl_radius / 2))
          && mouse.y < (rings[i].ctrl_position.y + (rings[i].ctrl_radius / 2))){
          rings[i].selected = true;
          dragging = true;
        }
      }
    }
  }else{
    for(var i = 0; i < rings.length; i++){
      rings[i].selected = false;
      dragging = false;
    }
  }

  outlet(0,"bang");
  mgraphics.redraw();
}

function onresize(){
  if(getWidth() < 300 || getHeight < 300){
      hiddenText = true;
      for(var i = 0; i < rings.length; i++){
        rings[i].ctrl_radius = 0.2;
      }
  }else{
      hiddenText = false;
      for(var i = 0; i < rings.length; i++){
        rings[i].ctrl_radius = 0.08;
      }
  }
}

//------------------------------------------------------------------------------
// CONVENIENCE
function getObject(name){
  return this.patcher.parentpatcher.getnamed(name);
}

function getWidth(){
  return this.box.rect[2] - this.box.rect[0];
}

function getHeight(){
  return this.box.rect[3] - this.box.rect[1];
}

function drawAlignmentCompass(alpha){
  with(mgraphics){
    //Main Boundary
    ellipse(-max_size,max_size,max_size*2,max_size*2);

    //Alignment Lines
    var line_point = new Point(0,max_size);
    for(var i = 0; i < 8; i++){
      move_to(0,0);
      line_to(line_point.x,line_point.y);
      line_point = line_point.rotate(Math.PI/4);
      stroke_with_alpha(alpha);
    }

    var line_point = new Point(0,max_size-0.2);
    line_point = line_point.rotate(Math.PI/8);
    for(var i = 0; i < 8; i++){
      move_to(0,0);
      line_to(line_point.x,line_point.y);
      line_point = line_point.rotate(Math.PI/4);
      stroke_with_alpha(alpha);
    }

    move_to(max_size - 0.05,0);
    set_line_width(0.1);
    rel_line_to(0.1,0);
    set_line_width(0.005);//set line width

    if(!hiddenText){
      move_to(max_size + 0.02,-text_measure("Θ = 0")[1]/getHeight());
      show_text("Θ = 0");
    }

    identity_matrix();
  }
}

function listMethods(object){
	var list = []
	list = Object.getOwnPropertyNames(object)
	    		.filter(function(property) {
	       			return typeof object[property] == 'function';
	    		}
	    		);
	post()
	post("# METHODS #")
	for(var i = 0 ; i < list.length ; i++){
		post()
		post(list[i]+"()")
	}
	post()
	post("# END #")
}

//------------------------------------------------------------------------------
// MATH UTILITIES
function getDistance(x1,y1,x2,y2){
  return Math.sqrt(Math.pow(x2-x1,2)+Math.pow(y2-y1,2));
}

function Point(x,y){
  this.x = x;
  this.y = y;

  this.rotate = function(theta){
    new_point = new Point();
    new_point.x = this.x * Math.cos(theta) - this.y * Math.sin(theta);
    new_point.y = this.x * Math.sin(theta) + this.y * Math.cos(theta);
    return new_point;
  }
}

function Color(r,g,b,a){
  this.r = r;
  this.g = g;
  this.b = b;
  this.a = a;
}

function map (value, a, b, c, d) {
    value = (value - a) / (b - a);
    return c + value * (d - c);
}
