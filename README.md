<!DOCTYPE html>
<html>
<head>
  <title>Mini jeu Phaser</title>
  <script src="https://cdn.jsdelivr.net/npm/phaser@3.55.2/dist/phaser.min.js"></script>
</head>
<body>

<script>
// Configuration du jeu
const config = {
  type: Phaser.AUTO,
  width: 800,
  height: 600,
  scene: {
    preload: preload,
    create: create,
    update: update
  }
};

// Initialisation du jeu
const game = new Phaser.Game(config);

// Variables globales
let player;
let cursors;
let items;
let score = 0;
let scoreText;

// Chargement des ressources
function preload() {
  this.load.image('background', 'chemin/vers/votre/image/fond.png');
  this.load.image('item', 'chemin/vers/votre/image/objet.png');
  this.load.spritesheet('player', 'chemin/vers/votre/spritesheet/personnage.png', { frameWidth: 32, frameHeight: 48 });
}

// Création des objets du jeu
function create() {
  // Ajouter le fond
  this.add.image(0, 0, 'background').setOrigin(0, 0);

  // Créer le personnage
  player = this.physics.add.sprite(100, 450, 'player');
  player.setCollideWorldBounds(true);

  // Animation du personnage
  this.anims.create({
    key: 'left',
    frames: this.anims.generateFrameNumbers('player', { start: 0, end: 3 }),
    frameRate: 10,
    repeat: -1
  });

  this.anims.create({
    key: 'turn',
    frames: [ { key: 'player', frame: 4 } ],
    frameRate: 20
  });

  this.anims.create({
    key: 'right',
    frames: this.anims.generateFrameNumbers('player', { start: 5, end: 8 }),
    frameRate: 10,
    repeat: -1
  });

  // Gérer les entrées du clavier
  cursors = this.input.keyboard.createCursorKeys();

  // Créer les objets à collecter
  items = this.physics.add.group({
    key: 'item',
    repeat: 11,
    setXY: { x: 12, y: 0, stepX: 70 }
  });

  // Ajouter une physique aux objets
  items.children.iterate(item => {
    item.setBounceY(Phaser.Math.FloatBetween(0.4, 0.8));
  });

  // Afficher le score
  scoreText = this.add.text(16, 16, 'Score: 0', { fontSize: '32px', fill: '#fff' });

  // Gérer les collisions entre le joueur et les objets
  this.physics.add.collider(player, items, collectItem, null, this);
}

// Mise à jour du jeu
function update() {
  // Déplacer le joueur avec les touches fléchées
  if (cursors.left.isDown) {
    player.setVelocityX(-160);
    player.anims.play('left', true);
  } else if (cursors.right.isDown) {
    player.setVelocityX(160);
    player.anims.play('right', true);
  } else {
    player.setVelocityX(0);
    player.anims.play('turn');
  }

  // Permettre au joueur de sauter s'il touche le sol
  if (cursors.up.isDown && player.body.touching.down) {
    player.setVelocityY(-330);
  }
}

// Fonction appelée lorsqu'un objet est collecté
function collectItem(player, item) {
  item.disableBody(true, true);
  score += 10;
  scoreText.setText('Score: ' + score);
}
</script>

</body>
</html>
