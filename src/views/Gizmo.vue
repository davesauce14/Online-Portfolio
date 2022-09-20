<template>
    <v-container fluid class="big-iframe">
      <v-row class="mb-6 big-iframe" no-gutters>
        <v-col sm="12" lg="12">
          <canvas id="renderCanvas" class="big-iframe"></canvas>
        </v-col>
      </v-row>
    </v-container>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import {  UtilityLayerRenderer, Scene, StandardMaterial, TransformNode, CylinderBuilder, Color3, Mesh, LinesMesh, Vector3 }from 'babylonjs'
import * as BABYLON from 'babylonjs'
import { EnhancedPositionGizmo } from '../models/EnhancedPositionGizmo';

@Component({})
export default class Gizmo extends Vue {

    // meshList: Mesh[] = [];
    meshMap: Map<Mesh, any> = new Map();
    dragging = false;

    mounted() {
        this.createScene();
    }

    createScene() {
        // Create scene
        const canvas: any =  document.getElementById("renderCanvas");
        const engine = new BABYLON.Engine( canvas);
        const scene = new BABYLON.Scene(engine);
        const camera = new BABYLON.ArcRotateCamera("Camera", -Math.PI / 4, Math.PI / 2.5, 10, BABYLON.Vector3.Zero(), scene);
        camera.attachControl(canvas, true);
        camera.minZ = 0.1;

        // Initialize GizmoManager
        const gizmoManager = new BABYLON.GizmoManager(scene);
        gizmoManager.utilityLayer.utilityLayerScene

        // Initialize all gizmos
        gizmoManager.positionGizmoEnabled = true;
        // gizmoManager.boundingBoxGizmoEnabled=true;
        // gizmoManager.rotationGizmoEnabled = true;
        // gizmoManager.scaleGizmoEnabled = true;
        const util = new UtilityLayerRenderer(scene);
        // gizmoManager.gizmos.positionGizmo = new EnhancedPositionGizmo(util);

        const utilityScene = gizmoManager.utilityLayer.utilityLayerScene;

        const thickness = .7;

        // Set Custom Mesh X
        const xColor = this.colorHelper(Color3.Red(), utilityScene);
        gizmoManager?.gizmos?.positionGizmo?.xGizmo?.setCustomMesh(this._CreateArrow(utilityScene, Color3.Red(), thickness, 'x'));
        
        // // Set Custom Mesh Y
        const yColor = this.colorHelper(Color3.Green(), utilityScene)
        gizmoManager?.gizmos?.positionGizmo?.yGizmo?.setCustomMesh(this._CreateArrow(utilityScene, Color3.Green(), thickness, 'y'));

        // Set Custom Mesh Z
        const zColor = this.colorHelper(Color3.Blue(), utilityScene)
        gizmoManager?.gizmos?.positionGizmo?.zGizmo?.setCustomMesh(this._CreateArrow(utilityScene, Color3.Blue(), thickness, 'z'));


        this.positionGizmoSyncLogic(utilityScene);

        console.log(this.meshMap);


        // // Modify gizmos based on keypress
        // document
        // .onkeydown = function(e) {
        //     if(e.key === 'w' || e.key === 'e'|| e.key === 'r'|| e.key === 'q'){
        //         // Switch gizmo type
        //         gizmoManager.positionGizmoEnabled = false;
        //         gizmoManager.rotationGizmoEnabled = false;
        //         gizmoManager.scaleGizmoEnabled = false;
        //         gizmoManager.boundingBoxGizmoEnabled = false;
        //         if(e.key == 'w'){
        //             gizmoManager.positionGizmoEnabled = true;
        //         }
        //         if(e.key == 'e'){
        //             gizmoManager.rotationGizmoEnabled = true;
        //         }
        //         if(e.key == 'r'){
        //             gizmoManager.scaleGizmoEnabled = true;
        //         }
        //         if(e.key == 'q'){
        //             gizmoManager.boundingBoxGizmoEnabled = true;
        //         }
        //     }
        //     if(e.key == 'y'){
        //         // hide the gizmo
        //         gizmoManager.attachToMesh(null);
        //     }
        //     if(e.key == 'a'){
        //         // Toggle local/global gizmo rotation positioning
        //         gizmoManager.gizmos.positionGizmo.updateGizmoRotationToMatchAttachedMesh = !gizmoManager.gizmos.positionGizmo.updateGizmoRotationToMatchAttachedMesh;
        //         gizmoManager.gizmos.rotationGizmo.updateGizmoRotationToMatchAttachedMesh = !gizmoManager.gizmos.rotationGizmo.updateGizmoRotationToMatchAttachedMesh;
        //     }
        //     if(e.key == 's'){
        //         // Toggle distance snapping
        //         if(gizmoManager.gizmos.scaleGizmo.snapDistance == 0){
        //             gizmoManager.gizmos.scaleGizmo.snapDistance = 0.3;
        //             gizmoManager.gizmos.rotationGizmo.snapDistance = 0.3;
        //             gizmoManager.gizmos.positionGizmo.snapDistance = 0.3;
        //         }else{
        //             gizmoManager.gizmos.scaleGizmo.snapDistance = 0;
        //             gizmoManager.gizmos.rotationGizmo.snapDistance = 0;
        //             gizmoManager.gizmos.positionGizmo.snapDistance = 0;
        //         }
        //     }
        //     if(e.key == 'd'){
        //         // Toggle gizmo size
        //         if(gizmoManager.gizmos.scaleGizmo.scaleRatio == 1){
        //             gizmoManager.gizmos.scaleGizmo.scaleRatio = 1.5;
        //             gizmoManager.gizmos.rotationGizmo.scaleRatio = 1.5;
        //             gizmoManager.gizmos.positionGizmo.scaleRatio = 1.5;
        //         }else{
        //             gizmoManager.gizmos.scaleGizmo.scaleRatio = 1;
        //             gizmoManager.gizmos.rotationGizmo.scaleRatio = 1;
        //             gizmoManager.gizmos.positionGizmo.scaleRatio = 1;
        //         }
        //     }
        // }

        // Start by only enabling position control
        // document.onkeydown({key: "w"})

        // Create objects
        const hdrTexture = BABYLON.CubeTexture.CreateFromPrefilteredData("https://playground.babylonjs.com/textures/environment.dds", scene);
        const hdrSkybox = BABYLON.Mesh.CreateBox("hdrSkyBox", 1000.0, scene);
        hdrSkybox.isPickable = false;
        const hdrSkyboxMaterial = new BABYLON.PBRMaterial("skyBox", scene);
        hdrSkyboxMaterial.backFaceCulling = false;
        hdrSkyboxMaterial.reflectionTexture = hdrTexture.clone();
        hdrSkyboxMaterial.reflectionTexture.coordinatesMode = BABYLON.Texture.SKYBOX_MODE;
        hdrSkyboxMaterial.microSurface = 1.0;
        hdrSkyboxMaterial.disableLighting = true;
        hdrSkybox.material = hdrSkyboxMaterial;
        hdrSkybox.infiniteDistance = true;
        const sphereGlass = BABYLON.Mesh.CreateSphere("sphereGlass", 48, 1.0, scene);
        sphereGlass.translate(new BABYLON.Vector3(1, 0, 0), -3);
        const sphereMetal = BABYLON.Mesh.CreateSphere("sphereMetal", 48, 1.0, scene);
        sphereMetal.translate(new BABYLON.Vector3(1, 0, 0), 3);
        const spherePlastic = BABYLON.Mesh.CreateSphere("spherePlastic", 48, 1.0, scene);
        spherePlastic.translate(new BABYLON.Vector3(0, 0, 1), -3);
        const woodPlank = BABYLON.MeshBuilder.CreateBox("plane", { width: 3, height: 0.1, depth: 3 }, scene);
        const glass = new BABYLON.PBRMaterial("glass", scene);
        glass.reflectionTexture = hdrTexture;
        glass.refractionTexture = hdrTexture;
        glass.linkRefractionWithTransparency = true;
        glass.indexOfRefraction = 0.52;
        glass.alpha = 0;
        glass.microSurface = 1;
        glass.reflectivityColor = new BABYLON.Color3(0.2, 0.2, 0.2);
        glass.albedoColor = new BABYLON.Color3(0.85, 0.85, 0.85);
        sphereGlass.material = glass;
        const metal = new BABYLON.PBRMaterial("metal", scene);
        metal.reflectionTexture = hdrTexture;
        metal.microSurface = 0.96;
        metal.reflectivityColor = new BABYLON.Color3(0.85, 0.85, 0.85);
        metal.albedoColor = new BABYLON.Color3(0.01, 0.01, 0.01);
        sphereMetal.material = metal;
        const plastic = new BABYLON.PBRMaterial("plastic", scene);
        plastic.reflectionTexture = hdrTexture;
        plastic.microSurface = 0.96;
        plastic.albedoColor = new BABYLON.Color3(0.206, 0.94, 1);
        plastic.reflectivityColor = new BABYLON.Color3(0.003, 0.003, 0.003);
        spherePlastic.material = plastic;
        const wood = new BABYLON.PBRMaterial("wood", scene);
        wood.reflectionTexture = hdrTexture;
        wood.environmentIntensity = 1;
        wood.specularIntensity = 0.3;
        wood.reflectivityTexture = new BABYLON.Texture("https://playground.babylonjs.com/textures/reflectivity.png", scene);
        wood.useMicroSurfaceFromReflectivityMapAlpha = true;
        wood.albedoColor = BABYLON.Color3.White();
        wood.albedoTexture = new BABYLON.Texture("https://playground.babylonjs.com/textures/albedo.png", scene);
        woodPlank.material = wood;

        engine.runRenderLoop(function() {
            scene.render();
        });

        return scene;
    }

    _CreateArrow(scene: Scene, color: Color3, thickness = 1, axis: string): any {

        // Parent
        const arrow = new TransformNode("arrow", scene);

        // Geometry
        const cylinder = CylinderBuilder.CreateCylinder("cylinder", { diameterTop: 0, height: 0.075, diameterBottom: 0.0375 * (1 + (thickness - 1) / 4), tessellation: 96 }, scene);
        const cylinderBox = CylinderBuilder.CreateCylinder("ignore", { diameterTop: 0.1 * (1 + (thickness - 1) / 4), height: 0.1, diameterBottom: 0.1 * (1 + (thickness - 1) / 4), tessellation: 96 }, scene);
        const line = CylinderBuilder.CreateCylinder("cylinder", { diameterTop: 0.005 * thickness, height: 0.275, diameterBottom: 0.005 * thickness, tessellation: 96 }, scene);
        const lineBox = CylinderBuilder.CreateCylinder("ignore", { diameterTop: 0.07 * thickness, height: 0.275, diameterBottom: 0.07 * thickness, tessellation: 96 }, scene);

        // Material
        const material = this.colorHelper(color, scene);
        const hoverMaterial = this.colorHelper(Color3.Yellow(), scene);
        const invisibleMaterial = this.colorHelper(Color3.Gray(), scene);

        // const hoverMaterial = new StandardMaterial("", scene);
        // hoverMaterial.diffuseColor = color.add(new Color3(0.3, 0.3, 0.3));
        invisibleMaterial.alpha = 0;

        // Position arrow pointing in its drag axis
        cylinder.material = material;
        cylinder.rotation.x = Math.PI / 2;
        cylinder.position.z += 0.3;
        cylinder.parent = arrow;

        line.material = material;
        line.position.z += 0.275 / 2;
        line.rotation.x = Math.PI / 2;
        line.parent = arrow;

        // For larger Bounding Border
        cylinderBox.rotation.x = Math.PI / 2;
        cylinderBox.position.z += 0.3;
        cylinderBox.material = invisibleMaterial;
        cylinderBox.parent = arrow;

        lineBox.material = invisibleMaterial;
        lineBox.position.z += 0.275 / 2;
        lineBox.rotation.x = Math.PI / 2;
        lineBox.parent = arrow;

        arrow.scaling.scaleInPlace(1 / 3);
        //arrow.lookAt(new Vector3(0,0,0).add(new Vector3(0,0,1)));

        if (axis === 'x') {
            arrow.rotation.y = Math.PI / 2;
            arrow.rotation.z = Math.PI / 2;
        } else if (axis === 'y') {
            // mesh.rotation.y = Math.PI / 2;
            arrow.rotation.x = Math.PI / 2 * -1;
        }

        const temp = {
          material,
          hoverMaterial,
          name: axis,
          active: false
        }
        this.meshMap.set(arrow as any, temp)
        // this.meshList.push(arrow as any);
        return arrow;
    }

    colorHelper(color: Color3, scene: Scene) {
      // todo: add mats to array and destroy gracefully
      const mat = new StandardMaterial("", scene);
      mat.diffuseColor = color;
      mat.emissiveColor = color;
      mat.specularColor = color.subtract(new Color3(0.1, 0.1, 0.1));
      return mat;
    }

    positionGizmoSyncLogic (scene: Scene) {

      const nonHovered = this.colorHelper(Color3.Gray(), scene);
      nonHovered.alpha = .4;


      const _pointerObserver = scene.onPointerObservable.add((pointerInfo) => {
        if(pointerInfo.pickInfo) {

          // On Hover Logic
          if(pointerInfo.type === BABYLON.PointerEventTypes.POINTERMOVE) {

            if(this.dragging) return;
            this.meshMap.forEach((statusMap, parentMesh) => {

              const isHovered = pointerInfo.pickInfo && (parentMesh.getChildMeshes().indexOf((pointerInfo.pickInfo.pickedMesh as Mesh)) != -1);
              const material = isHovered || statusMap.active ? statusMap.hoverMaterial : statusMap.material;
              parentMesh.getChildMeshes().forEach((m) => {
                  if(m.name !== 'ignore') {
                      m.material = material;
                      if ((m as LinesMesh).color) {
                          (m as LinesMesh).color = material.diffuseColor;
                      }
                  }
              });
            })
                
          }

          // On Mouse Down
          if(pointerInfo.type === BABYLON.PointerEventTypes.POINTERDOWN) {

            // If user Clicked Gizmo
            if(this.meshMap.has(pointerInfo.pickInfo.pickedMesh?.parent as Mesh)) {
              this.dragging = true;
              const statusMap = this.meshMap.get(pointerInfo.pickInfo.pickedMesh?.parent as Mesh);
              statusMap.active = true;
              this.meshMap.forEach((statusMap, parentMesh) => {

                const isHovered = pointerInfo.pickInfo && (parentMesh.getChildMeshes().indexOf((pointerInfo.pickInfo.pickedMesh as Mesh)) != -1);
                const material = isHovered || statusMap.active ? statusMap.hoverMaterial : nonHovered;
                parentMesh.getChildMeshes().forEach((m) => {
                    if(m.name !== 'ignore') {
                        m.material = material;
                        if ((m as LinesMesh).color) {
                            (m as LinesMesh).color = material.diffuseColor;
                        }
                    }
                });
              })
            }
          }

          // On Mouse Down
          if(pointerInfo.type === BABYLON.PointerEventTypes.POINTERUP) {
            console.log(this.meshMap)
            this.meshMap.forEach((statusMap, parentMesh) => {
              statusMap.active = false;
              this.dragging = false;
              parentMesh.getChildMeshes().forEach((m) => {
                  if(m.name !== 'ignore') {
                      m.material = statusMap.material;
                      if ((m as LinesMesh).color) {
                          (m as LinesMesh).color = statusMap.material.diffuseColor;
                      }
                  }
              });
            })
          }
        }
      });
    }
}
</script>
