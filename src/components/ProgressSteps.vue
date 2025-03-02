<template>
    <h1>Pasos de progreso con Vue 3</h1>
    <div class="progrees-steps">

        <div class="step-number" v-for="(step, index) in steps" :key="index" :class="{ 'step-active': index === currentStep }">
           {{ step.step }}
        </div>

        <div class="step-content" v-for="(step, index) in steps" :key="index" >
            <div v-if="index === currentStep">
                <h3>{{ step.title }}</h3>
                <p>{{ step.description }}</p>
            </div>
        </div>
        
    </div>
    
    <div class="controls">
        <button @click="prevStep" :disabled="currentStep === 0" class="btn-prev">Previous</button>
        <button @click="nextStep" :disabled="currentStep === steps.length - 1" class="btn-next">Next</button>
    </div>
    
</template>

<script setup>
import { ref } from 'vue';

const steps = ref([
    {step: 1, title: 'Sacudir', description: 'Agitar bruscamente para quitarle el polvo.'},
    {step: 2, title: 'Barrer', description: 'Limpiar un lugar arrastrando la suciedad y lo sacudido con algo como una escoba.'},
    {step: 3, title: 'Trapear', description: 'Fregar el suelo con trapo o estropajo, después de barrer.'}
])

const currentStep = ref(0)

const nextStep = () => {
    if( currentStep.value < steps.value.length - 1 ) {
       currentStep.value++
    }
}

const prevStep = () => {
    if ( currentStep.value > 0 ) {
        currentStep.value--
    }
}

</script>

<style scoped>

h1 {
    margin-bottom: 40px;
    text-align: center;
}

h3 {
    font-weight: bolder;
}

.step-active {
    background-color: grey;
    color: white;
}

.step-number {
    margin-top: 20px;
    border: 1px solid grey;
    margin: 8px;
    padding: 8px;
    display: inline;
    width: 60px;
    height: 40px;

}

.step-content {
    margin: 40px 10px;
}

.progrees-steps {
    margin: 10px;
    width: 400px;
    height: 200px;
}

.controls {
    margin: 10px;
    width: 400px;
    height: 40px;
}

.btn-prev {
    float: left;
}

.btn-next {
   float: right;
}

</style>
