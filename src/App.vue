<template>
  <div>
    <!-- <div class="canvas-container" ref="canvas"></div> -->
  </div>
</template>

<script setup>
import HavokPhysics from "@babylonjs/havok";
import {
  Engine,
  Scene,
  FreeCamera,
  Vector3,
  SpotLight,
  HemisphericLight,
  SceneLoader,
  MeshBuilder,
  DirectionalLight,
  ArcRotateCamera,
  HavokPlugin,
  DeviceSourceManager,
  ActionManager,
  ExecuteCodeAction,
  AssetContainer
} from "@babylonjs/core";
import "@babylonjs/loaders/glTF";

// 创建canvas
const canvas = document.createElement("canvas");

// 设置canvas的宽高
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
// 将canvas添加到body中
document.body.appendChild(canvas);
// 创建引擎
const engine = new Engine(canvas, true);

const createScene = async function () {
  // 创建场景
  const scene = new Scene(engine);
  // 创建相机
  const camera = new ArcRotateCamera("camera", 0, 0, 10, Vector3.Zero(), scene);
  // 设置相机的位置
  camera.setPosition(new Vector3(0, 0, -10));
  // 创建设备管理器
  const deviceSourceManager = new DeviceSourceManager(scene.getEngine());
  // 把相机附加到画布上
  camera.attachControl(canvas);
  const light3 = new HemisphericLight("HemiLight", new Vector3(0, 1, 0), scene);
  // const light2 = new BABYLON.PointLight('light', new BABYLON.Vector3(0, 1, 0), scene);
  // const light = new BABYLON.HemisphericLight('light', new BABYLON.Vector3(0, 1, 0), scene);
  // const sphere = MeshBuilder.CreateSphere("sphere", { diameter: 1 }, scene);
  // 创建地面
  const ground = MeshBuilder.CreateGround(
    "ground",
    { width: 10, height: 10 },
    scene
  );
  
  // initialize the plugin using the HavokPlugin constructor
  // const havokPlugin = HavokPhysics();
  const havokInstance = await HavokPhysics();
  const gravityVector = new Vector3(0, -9.81, 0);
  const physicsPlugin = new HavokPlugin(true, havokInstance);
  scene.enablePhysics(gravityVector, physicsPlugin);
  // 加载gltf模型
  SceneLoader.Append("models/", "Xbot.glb", scene, (gltf) => {
    console.log(gltf);
  });

  const inputMap = {};
  scene.actionManager = new ActionManager(scene);
  scene.actionManager.registerAction(
    new ExecuteCodeAction(
      ActionManager.OnKeyDownTrigger,
      function (evt) {
        inputMap[evt.sourceEvent.key] = evt.sourceEvent.type == "keydown";
      }
    )
  );
  scene.actionManager.registerAction(
    new ExecuteCodeAction(
      ActionManager.OnKeyUpTrigger,
      function (evt) {
        inputMap[evt.sourceEvent.key] = evt.sourceEvent.type == "keydown";
      }
    )
  );

  function initPlayer() {
        let meshContent = AssetContainer;
        const boxHelper = MeshBuilder.CreateBox('lbl', { height: 3.2 }, this.scene);
        boxHelper.visibility = 0;
        boxHelper.position.y = 3;
        container.animationGroups.forEach((item, index) => {
            item.play(true);
            if (index === AnimationKey.Idle) {
                item.setWeightForAllAnimatables(1);
            } else {
                item.setWeightForAllAnimatables(0);
            }
        });
        const player = MeshBuilder.CreateCapsule(
            'lbl',
            { height: 3.6, radius: 0.5 },
            this.scene
        );
        player.visibility = 0;
        const [mesheRoot] = container.meshes;
        mesheRoot.position.y = 1.17;
        mesheRoot.position.y = 18 - 1.8;
        mesheRoot.position.z = -5;
        player.checkCollisions = true;
        mesheRoot.scaling = new BABYLON.Vector3(2, 2, 2);
        player.position.y = 18;
        player.position.z = -5;
        player.addChild(mesheRoot);
        this.player = player;

        // eslint-disable-next-line @typescript-eslint/ban-ts-comment
        // @ts-ignore
        this.playerFeetRay = new BABYLON.Ray();
        const rayHelper = new BABYLON.RayHelper(this.playerFeetRay);
        rayHelper.attachToMesh(mesheRoot, new BABYLON.Vector3(0, -1, 0), undefined, 0.1);
        rayHelper.show(this.scene); // 脚底光线

        const aggregate = new BABYLON.PhysicsAggregate(
            player,
            BABYLON.PhysicsShapeType.CAPSULE,
            { mass: 1, friction: 0.5, restitution: 0 },
            this.scene
        );

        aggregate.body.setMotionType(BABYLON.PhysicsMotionType.DYNAMIC);
        aggregate.body.disablePreStep = false;
        aggregate.body.setMassProperties({
            inertia: new BABYLON.Vector3(0, 0, 0),
        });
        this.camera.setTarget(player);
    }

  if (inputMap["w"]) {
  hero.moveWithCollisions(hero.forward.scaleInPlace(heroSpeed));
  keydown = true;
}
if (inputMap["s"]) {
  hero.moveWithCollisions(hero.forward.scaleInPlace(-heroSpeedBackwards));
  keydown = true;
}
if (inputMap["a"]) {
  hero.rotate(Vector3.Up(), -heroRotationSpeed);
  keydown = true;
}
if (inputMap["d"]) {
  hero.rotate(Vector3.Up(), heroRotationSpeed);
  keydown = true;
}
if (inputMap["b"]) {
  keydown = true;
}
  return scene;
};

// 渲染场景
createScene().then((scene) => {
  engine.runRenderLoop(() => {
    if (scene) {
      scene.render();
    }
  });
});

// 监听窗口大小改变
window.addEventListener("resize", () => {
  engine.resize();
});
</script>

<style>
* {
  width: 0;
  height: 0;
}
canvas {
  display: block;
  position: absolute;
  left: 0;
  top: 0;
  width: 100vw;
  height: 100vh;
}
/* .canvas-dom{
  width: 100%;
  height: 100%;
} */
</style>
