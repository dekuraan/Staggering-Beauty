soundManager.setup({
  preferFlash: true,
  flashVersion: 9,
  useHighPerformance: true,
  wmode: 'transparent',
  debugMode: false
});

var audio, numLoaded = 0;
soundManager.onready(function() {
  audio = soundManager.createSound({
    id: 'peaking',
    url: 'drums.mp3',
    multiShot: true,
    autoLoad: true,
    onload: function() {
      numLoaded++;
      audio.play();
      audio.setVolume(0);
    },
    onfinish: function() {
      audio.play();
    }
  });
  audio2 = soundManager.createSound({
    id: 'peaking2',
    url: 'texture.mp3',
    multiShot: true,
    autoLoad: true,
    onload: function() {
      numLoaded++;
      audio2.play();
      audio2.setVolume(0);
    },
    onfinish: function() {
      audio2.play();
    }
  });

});

var img = new Image();
img.src = '../assets/bg.gif';

var embedButton = document.getElementById('embed-button');
var embedShade = document.getElementById('embed');
var embedText = document.getElementById('embed-text');

embedButton.addEventListener('click', function(e) {
  e.preventDefault();
  embedShade.style.display = 'block';
  return false;
}, false);

embedShade.addEventListener('click', function() {
  embedShade.style.display = 'none';
}, false);

embedText.addEventListener('click', function(e) {
  e.preventDefault();
  e.stopPropagation();
  this.select();
  return false;
})

var restDrag = 0.8;
var physics = new ParticleSystem(+0.34, -3, 0, restDrag);

var params = {
  // mouseXWidth: 0.5,
  // mouseYMin: 0.84,
  mouseTargetEasing: 0.66,
  mouseTargetEasingUp: 0.66,
  needsDrag: false,
  fillColor: '#000',
  strokeColor: '#000'// 0.1
};


var paths = [];
var particles = [];
var supports = [];
var supports2 = [];
var springs = [];

var mouseDown = false;

var firstPlay = true;
var frameCount = 0;

var numPoints = 8;
var mouthPadding = 2;

var idleTimeout;
var idleTimeoutLength = 15000;

var bend = -0.015;
var smooth = true;

var maxForce = 30;

var segmentLength = getSegmentLength();
var mouthPoints = numPoints-mouthPadding*2;

var thickness = 110;
var strokeWeight = 30;

var prevEyeRotation = 0;
var hue = 0;

var padding = (thickness+strokeWeight)/2; 
var peaking = false;
var prevPeaking = false;

var lastPoint;

var stress = 0;

var mousePos = new Point(view.size.width/2, view.size.height-segmentLength);
var targetMousePos = new Point(view.size.width/2, view.size.height-segmentLength);
var pathShadow = new Path();

var pathBody = new Path();
pathBody.style = {
  strokeColor: params.fillColor,
  strokeWidth: thickness + strokeWeight,
  strokeCap: 'round'
};
// pathBody.fullySelected = true;
paths.push(pathBody);

// buildGUI();

// var pathBodyStroke = new Path();
// pathBodyStroke.style = {
//   strokeColor: params.strokeColor,
//   strokeWidth: thickness,
//   strokeCap: 'round'
// };
// pathBodyStroke.opacity = 0;
// paths.push(pathBodyStroke);

// pathShadow.style = {
//   strokeColor: '#000',
//   strokeWidth: thickness + strokeWeight,
//   strokeCap: 'round'
// };
// pathShadow.opacity = 0.2;
// paths.push(pathShadow);

var pathMouth = new Path();
pathMouth.style = {
  strokeColor: '#f03',
  strokeCap: 'round'
};
pathMouth.opacity = 0;
paths.push(pathMouth);

var pathTongue = new Path();
pathTongue.style = {
  strokeColor: '#AB2259',
  strokeCap: 'round'
};
pathTongue.opacity = 0;
paths.push(pathTongue)

// var eye1, eye2;

buildMouth();
buildBody();

var eyeHeight = { s: 1 };

var blinkTween1 = new TWEEN.Tween(eyeHeight).to({ s: 0 }, 90).easing(TWEEN.Easing.Exponential.EaseIn);
var blinkTween2 = new TWEEN.Tween(eyeHeight).to({ s: 1 }, 140).easing(TWEEN.Easing.Exponential.EaseIn);
var blinkTween3 = new TWEEN.Tween(eyeHeight).to({ s: 0 }, 60).easing(TWEEN.Easing.Exponential.EaseIn);
var blinkTween4 = new TWEEN.Tween(eyeHeight).to({ s: 1 }, 180).easing(TWEEN.Easing.Exponential.EaseIn);

blinkTween1.chain(blinkTween2);
blinkTween2.chain(blinkTween3);
blinkTween3.chain(blinkTween4);
blinkTween4.chain(blinkTween1);
blinkTween1.delay(9000);
blinkTween1.start();

if (navigator.userAgent.match(/(iPad|iPhone|iPod|Android)/i)) {
  // document.getElementById('compat').style.display = 'block';
}

wrong = setTimeout(function() {
  // document.getElementById('compat').style.display = 'none';
  document.getElementById('shake').style.display = 'block';
}, 15000);

// hack for 1px line
document.getElementById('canvas').height++;
document.getElementById('canvas').width++;

var eyeJiggler = new Jiggler(6);
eyeJiggler.k = 4;
eyeJiggler.mass = 5;

var firstRun = true;
function onFrame(event) {
  Jiggler.update();
  frameCount++;
  TWEEN.update();
  updatePaths();
  firstRun = false;
};

setInterval(function() {
  physics.gravity.x = Math.sin(Date.now()/4000)*0.4;
  physics.gravity.y =  Math.sin(Date.now()/6000)*0.4 - 3
  updateAppearance();
  setPositions();
  physics.tick(1.0);
}, 1000/60);

var eye1Pos = new Point(), eye2Pos = new Point();

function onDraw(ctx) {

  var segment = pathBody.segments[numPoints - 2];
  var angle = segment.angle + Math.PI/2;

  var shake = (Math.random()-Math.random()) * 10;
  var radius = Math.max(0, eyeJiggler.pos);//Math.max(0, 5 + (peaking ? 10 + shake : 1));

  eye1Pos.x = segment.point.x;
  eye1Pos.y = segment.point.y;
  eye1Pos.x += Math.cos(angle)*thickness/2.4;
  eye1Pos.y += Math.sin(angle)*thickness/2.4;
  // eye1Pos.x -= Math.cos(angle-Math.PI/2)*thickness/6;
  // eye1Pos.y -= Math.sin(angle-Math.PI/2)*thickness/6;

  eye2Pos.x = segment.point.x;
  eye2Pos.y = segment.point.y;
  eye2Pos.x -= Math.cos(angle)*thickness/12;
  eye2Pos.y -= Math.sin(angle)*thickness/12;
  // eye2Pos.x -= Math.cos(angle-Math.PI/2)*thickness/6;
  // eye2Pos.y -= Math.sin(angle-Math.PI/2)*thickness/6;

// if (peaking) {
//   ctx.shadowColor = '#f0c';
//   ctx.shadowBlur = 20;
//   ctx.shadowOffsetY = 5;
// }

  ctx.fillStyle = '#fff';// peaking ?  'hsl('+Math.round(hue)%360+', 100%, 50%)' : '#fff';
  ctx.save();
  ctx.translate(eye1Pos.x, eye1Pos.y);
  ctx.rotate(angle);
  ctx.scale(1, peaking ? 1 : eyeHeight.s);
  circle(ctx, radius)
  ctx.restore();

  ctx.save();
  ctx.translate(eye2Pos.x, eye2Pos.y);
  ctx.rotate(angle);
  ctx.scale(peaking ? 1.2 : 1, peaking ? 1.2 : eyeHeight.s);
  // circle(ctx, radius, peaking ? Math.PI*2 : 0, peaking ? Math.PI*0.8 : Math.PI*2);
  circle(ctx, radius);
  ctx.restore();
  
  ctx.shadowBlur = 0;
  ctx.shadowOffsetY = 0;


}

function onMouseDown(event) {
  mouseDown = true;
}

function onMouseUp(event) {
  mouseDown = false;
  if (params.needsDrag) {
    targetMousePos.x = view.size.width/2;
    targetMousePos.y = view.size.height-segmentLength;
  }
}

window.addEventListener('mouseout', function() {
  // targetMousePos.x = view.size.width/2;
  // targetMousePos.y = view.size.height-segmentLength;
}, false);


function onMouseMove(event) {

  if (params.needsDrag && !mouseDown) {
    return;
  }

  var a = Math.atan2(event.point.y - view.size.height, event.point.x - view.size.width/2);

  targetMousePos.x = view.size.width/2 + Math.cos(a) * segmentLength*3;
  targetMousePos.y = view.size.height + Math.sin(a) * segmentLength;

  // clearTimeout(idleTimeout);
  // idleTimeout = setTimeout(function() {
  //   onResize();
  // }, idleTimeoutLength);

}

function onResize() {
  document.getElementById('canvas').height++;
  document.getElementById('canvas').width++;
  segmentLength = getSegmentLength();
  particles[0].position.x = view.size.width/2;
  particles[0].position.y = view.size.height + segmentLength;
  targetMousePos.x = view.size.width/2;
  targetMousePos.y = view.size.height - segmentLength;
  for (var i = 0; i < springs.length; i++) {
    springs[i].length = segmentLength;
  }
}

function getSegmentLength() {
  return view.size.height/numPoints * 0.7;
}

function buildMouth() {

  for (var i = 0; i < mouthPoints; i++) {
    pathMouth.add(new Point());
    if ( i < mouthPoints-2) {
      pathTongue.add(new Point());
    }
  }

}

function buildBody() {

  for (i = 0; i < numPoints; i++) {

    var x = view.size.width / 2;
    var y = view.size.height - (i-1)*segmentLength;

    var particle = physics.makeParticle(2.5, x, y, 0);
    var support = physics.makeParticle(1, x, y - segmentLength, 0);
    var support2 = physics.makeParticle(1, x, y + segmentLength, 0);

    if (i > 0) {
      var prevSupport = supports[i-1];
      var prevParticle = particles[i-1];
      physics.makeSpring(particle, prevSupport, 0.6, 0.48, 0);
      physics.makeSpring(prevParticle, support2, 0.3, 0.7, 0);
      springs.push(physics.makeSpring(particle, prevParticle, 0.2, 0.1, segmentLength));
    }

    if (i < 2) {
      particle.makeFixed();
    }

    support.makeFixed();
    support2.makeFixed();

    var point = new Point();
    pathBody.add(point);
    // pathBodyStroke.add(point);
    // pathShadow.add(point);

    particles.push(particle);
    supports.push(support);
    supports2.push(support2);

  }

}


function setPositions() {

  var e = mouseDown ? params.mouseTargetEasing : params.mouseTargetEasingUp;

  mousePos.x += (targetMousePos.x - mousePos.x) * e;
  mousePos.y += (targetMousePos.y - mousePos.y) * e;

  particles[1].position.x = mousePos.x;
  particles[1].position.y = mousePos.y;

  var targetStress = 0;

  for (var i = 1; i < numPoints; i++) {

    var support = supports[i];
    var curParticle = particles[i];

    curParticle.position.x = clamp(curParticle.position.x, padding, view.size.width-padding);

    var prevParticle = particles[i-1];
    var angle = Math.atan2(curParticle.position.y - prevParticle.position.y, curParticle.position.x - prevParticle.position.x);

    var force = curParticle.force.length();
    if (force > maxForce) {
      curParticle.force.scale(force / maxForce);
      
      force = maxForce;
    }

    if (i > 1) targetStress += force;

    support.position.x = curParticle.position.x + Math.cos(angle + i*bend)*segmentLength;
    support.position.y = curParticle.position.y + Math.sin(angle + i*bend)*segmentLength;

    pathBody.segments[i].angle = angle;

    var support2 = supports2[i];
    support2.position.x = curParticle.position.x + Math.cos(Math.PI + angle)*segmentLength;
    support2.position.y = curParticle.position.y + Math.sin(Math.PI + angle)*segmentLength;

  }

  stress += (targetStress-stress)*0.03;

}


function updateAppearance() {

  if (stress > 80) {

    hue += stress > 300 ? Math.pow(stress/5, 2) : stress;

    pathBody.strokeColor = 'hsl('+Math.round(hue)%360+', 100%, 50%)';
    pathMouth.strokeColor = 'hsl('+Math.round(hue)%360+', 100%, 90%)';
    pathTongue.strokeColor = 'hsl('+Math.round(hue)%360+', 100%, 50%)';
    pathMouth.strokeWidth = thickness/1.5 + (Math.random()-Math.random())*20;
    pathTongue.strokeWidth = pathMouth.strokeWidth*0.65;
    // pathShadow.strokeColor = '#000';
    peaking = true;
    clearTimeout(wrong);
    document.getElementById('shake').style.display = 'none';

  } else {


    pathBody.strokeColor = params.fillColor;
    // pathBodyStroke.strokeColor = params.strokeColor;
    // pathShadow.strokeColor = null;
    peaking = false;

  }


  if (peaking) {
    // document.body.className = Math.random() < 0.8 ? 'bg-a' : 'bg-b';
      document.body.className = frameCount % 3 != 0 ? 'bg-a' : 'bg-b';
    
    if (!prevPeaking) {
      physics.drag = 0.2;
      pathMouth.opacity = 1;
      pathTongue.opacity = 1;
      // unSmooth();
      eyeJiggler.rest = 17;
    }
    if (firstPlay && audio) {
      // audio.play({ loops: 1000 });
      // console.log('set volume');
      audio.setVolume(50);
      audio2.setVolume(70);
      firstPlay = false;
    }
  } else if (!peaking && prevPeaking) {
    document.body.className = '';
    physics.drag = restDrag;
    pathMouth.opacity = 0;
    pathTongue.opacity = 0;
    eyeJiggler.rest = 6;
    if (audio) {
      // audio.pause();
      audio.setVolume(0);
      audio2.setVolume(0);
      firstPlay = true;
    }
  }

  prevPeaking = peaking;

}

function updatePaths() {

  for (var i = 0, j, l; i < numPoints; i++) {

    var curParticle = particles[i];
    var prevParticle = particles[i-1];
    var angle = pathBody.segments[i].angle + Math.PI/2;

    pathBody.segments[i].point.x = curParticle.position.x;
    pathBody.segments[i].point.y = curParticle.position.y;

    // pathBodyStroke.segments[i].point.x = curParticle.position.x;
    // pathBodyStroke.segments[i].point.y = curParticle.position.y;

    j = i-mouthPadding+1;
    l = pathMouth.segments.length;

    if (j >= 0 && j < l) {

      pathMouth.segments[j].point.x = curParticle.position.x;// + (Math.random() - Math.random())*10;
      pathMouth.segments[j].point.y = curParticle.position.y;// + (Math.random() - Math.random())*10;
      pathMouth.segments[j].point.x += Math.cos(angle)*thickness/6;
      pathMouth.segments[j].point.y += Math.sin(angle)*thickness/10;

      if (pathTongue.segments[j]) {
        pathTongue.segments[j].point.x = pathMouth.segments[j].point.x;
        pathTongue.segments[j].point.y = pathMouth.segments[j].point.y;
        pathTongue.segments[j].point.x -= Math.cos(angle)*(pathMouth.strokeWidth/15);
        pathTongue.segments[j].point.y -= Math.sin(angle)*(pathMouth.strokeWidth/5);
      }

    }

    // pathShadow.segments[i].point.x = curParticle.position.x+50;
    // pathShadow.segments[i].point.y = curParticle.position.y-10;


  }

  if (smooth) {
    for (var i = 0, l = paths.length; i < l; i++) {
      paths[i].smooth();
    }
  }

}

function buildGUI() {
  
  var gui = new dat.GUI();

  gui.add(params, 'mouseXWidth', 0, 1);
  gui.add(params, 'mouseYMin', 0, 1);
  gui.add(params, 'mouseTargetEasing', 0, 1);
  gui.add(params, 'needsDrag');
  gui.addColor(params, 'fillColor');
  gui.addColor(params, 'strokeColor');
}

function unSmooth() {

  for (var j = 0, k = paths.length; j < k; j++) {
    var path = paths[j];
    for (var i = 0, l = path.segments.length; i < l; i++) {
      var segment = path.segments[i];
      segment.handleIn = segment.handleOut = null;
    }
  } 

}

function clamp(v, lo, hi) {
  if (v < lo) return lo;
  if (v > hi) return hi;
  return v;
}

// custom
var _draw = view.draw;
view.draw = function() {
  _draw.call(this);
  if (firstRun) return;
  onDraw(this._context);
}

function circle(ctx, radius, angle1, angle2) {
  ctx.beginPath();
  ctx.arc(0, 0, radius, angle1 || 0, angle2 || Math.PI*2, true);
  ctx.fill();
}