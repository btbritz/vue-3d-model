<template>
    <demo-block :vue-code="code" :html-code="htmlCode">
        <template slot="preview">
            <model-collada
                ref="collada"
                :rotation="rotation"
                @on-load="onLoad"
                src="static/models/collada/elf/elf.dae"></model-collada>
            <div class="example-loading" v-show="loading"></div>
        </template>
    </demo-block>
</template>

<script>
import DemoBlock from '../components/demo-block';
import ModelCollada from '../../src/model-collada.vue'

const code = `
<template>
    <model-collada
        :rotation="rotation"
        @on-load="onLoad"
        src="static/models/collada/elf.dae"></model-collada>
</template>

<script>
    import { ModelCollada } from 'vue-3d-model'

    export default {
        data: {
            rotation: {
                x: 0,
                y: 0,
                z: 0
            }
        },
        methods: {
            onLoad () {
                this.rotate();
            },
            rotate () {
                this.rotation.y += 0.01;
                requestAnimationFrame( this.rotate );
            }
        },
        components: {
            ModelCollada
        }
    }
<\/script>
`

const htmlCode = `
<body>
    <div id="app">
        <model-collada
            :rotation="rotation"
            @on-load="onLoad"
            src="static/models/collada/elf.dae"></model-collada>
    </div>
    #scripts#
    <script>
        new Vue({
            el: '#app',
            data: {
                rotation: {
                    x: 0,
                    y: 0,
                    z: 0
                }
            },
            methods: {
                onLoad () {
                    this.rotate();
                },
                rotate () {
                    this.rotation.y += 0.01;
                    requestAnimationFrame( this.rotate );
                }
            }
        })
    <\/script>
</body>
`

export default {
    name: 'demo-rotate',
    data () {
        return {
            code,
            htmlCode,
            loading: true,
            rotation: {
                x: 0,
                y: 0,
                z: 0
            }
        }
    },
    methods: {
        onLoad () {
            this.loading = false;
            this.rotate();
        },
        rotate () {
            requestAnimationFrame( this.rotate );
            this.rotation.y += 0.01;
        }
    },
    components: {
        ModelCollada,
        DemoBlock
    }
}
</script>