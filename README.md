const player = new THREE.Group();
// AUTO
const car = new THREE.Group();

const carBody = new THREE.Mesh(
  new THREE.BoxGeometry(2.2, 0.8, 4.2),
  new THREE.MeshStandardMaterial({color:0x111111, metalness:0.7, roughness:0.25})
);
carBody.position.y = 0.8;
car.add(carBody);

// Dak
const roof = new THREE.Mesh(
  new THREE.BoxGeometry(1.8, 0.8, 2),
  new THREE.MeshStandardMaterial({color:0x222222, metalness:0.5})
);
roof.position.set(0,1.55,-0.2);
car.add(roof);

// Wielen
for (const x of [-1.15,1.15]) {
  for (const z of [-1.3,1.3]) {
    const wheel = new THREE.Mesh(
      new THREE.CylinderGeometry(0.42,0.42,0.3,20),
      new THREE.MeshStandardMaterial({color:0x111111})
    );
    wheel.rotation.z=Math.PI/2;
    wheel.position.set(x,0.45,z);
    car.add(wheel);
  }
}

car.position.set(5,0,8);
scene.add(car);

let driving = false;
let carSpeed = 0;
function animate() {const distance=9;
// Instappen en uitstappen
if (keys["e"]) {
  if (!window.ePressed) {
    const d = player.position.distanceTo(car.position);
    if (driving || d < 5) {
      driving = !driving;
      if (!driving) {
        player.position.x=car.position.x+3;
        player.position.z=car.position.z;
      }
    }
    window.ePressed=true;
  }
} else {
  window.ePressed=false;
}

// Auto rijden
if (driving) {
  if (keys["w"]) carSpeed += 18*dt;
  if (keys["s"]) carSpeed -= 18*dt;

  carSpeed *= Math.pow(0.97,dt*60);
  carSpeed = Math.max(-12,Math.min(35,carSpeed));

  if (keys["a"]) car.rotation.y += 1.8*dt*Math.sign(carSpeed || 1);
  if (keys["d"]) car.rotation.y -= 1.8*dt*Math.sign(carSpeed || 1);

  car.position.x += Math.sin(car.rotation.y)*carSpeed*dt;
  car.position.z += Math.cos(car.rotation.y)*carSpeed*dt;

  player.position.copy(car.position);
} else {
  const distance=9;
  const target=new THREE.Vector3(
    player.position.x+Math.sin(angle)*distance,
    player.position.y+4+pitch*5,
    player.position.z+Math.cos(angle)*distance
  );
  camera.position.lerp(target,0.12);
  camera.lookAt(player.position.x,player.position.y+1.3,player.position.z);
}

// Camera bij auto
if (driving) {
  const target = new THREE.Vector3(
    car.position.x-Math.sin(car.rotation.y)*9,
    5,
    car.position.z-Math.cos(car.rotation.y)*9
  );
  camera.position.lerp(target,0.12);
  camera.lookAt(car.position.x,1,car.position.z);
}
