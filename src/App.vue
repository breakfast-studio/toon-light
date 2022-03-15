<template>
    <Lunchbox
        :cameraPosition="[0.25, 1.1, 3]"
        :cameraLook="[0, 0, 0]"
        background="cadetblue"
        shadow
    >
        <pointLight :position="lightCurrent" />

        <!-- light -->
        <mesh :position="lightCurrent" :scale="0.05">
            <icosahedronGeometry />
            <meshBasicMaterial color="white" />

            <mesh :scale="1.001">
                <icosahedronGeometry />
                <meshBasicMaterial color="black" wireframe />
            </mesh>
        </mesh>

        <!-- shape -->
        <mesh @pointerOver="onPointerOver">
            <octahedronGeometry />
            <meshToonMaterial />

            <mesh :scale="1.001">
                <octahedronGeometry />
                <meshBasicMaterial color="black" wireframe />
            </mesh>
        </mesh>
    </Lunchbox>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import * as THREE from 'three'
import { onBeforeRender } from 'lunchboxjs'

const lightDistance = 0.85
const lightCurrent = ref([0, lightDistance, 0])
const lightTarget = ref<[number, number, number]>([0, lightDistance, 0])
const worker = new THREE.Vector3()
const currentVector = new THREE.Vector3()

const onPointerOver = ({
    intersection,
}: {
    intersection: THREE.Intersection
}) => {
    // normalize difference
    const diff = intersection.point.clone()
    diff.add(intersection.face!.normal)
        .normalize()
        .multiplyScalar(lightDistance)
    lightTarget.value = [diff.x, diff.y, diff.z]
}

onBeforeRender(() => {
    worker.set(...lightTarget.value)
    currentVector.lerp(worker, 0.1)
    currentVector.normalize().multiplyScalar(lightDistance)
    lightCurrent.value = [currentVector.x, currentVector.y, currentVector.z]
})
</script>