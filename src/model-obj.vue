<script>
import { OBJLoader } from './loaders/OBJLoader'
import { MTLLoader } from './loaders/MTLLoader'
import { toIndexed } from './util'
import mixin from './model-mixin.vue'

export default {
    name: 'model-obj',
    mixins: [ mixin ],
    props: {
        lights: {
            type: Array,
            default () {
                return [
                    {
                        type: 'HemisphereLight',
                        position: { x: 0, y: 1, z: 0 },
                        skyColor: 0xaaaaff,
                        groundColor: 0x806060,
                        intensity: 0.2
                    },
                    {
                        type: 'DirectionalLight',
                        position: { x: 1, y: 1, z: 1 },
                        color: 0xffffff,
                        intensity: 0.8
                    },
                    {
                        type: 'DirectionalLight',
                        position: { x: 1, y: 100, z: 100 },
                        color: 0xffffff,
                        intensity: 0.8
                    },
                ]
            }
        },
        smoothing: {
            type: Boolean,
            default: false
        },
        mtlPath: {
            type: String
        },
        mtl: {
            type: String
        }
    },
    data () {
        return {
            loader: new OBJLoader(),
            mtlLoader: new MTLLoader()
        }
    },
    watch: {
        // mtl () {
        //     this.load();
        // }
    },
    methods: {
        process ( object ) {
            if ( this.smoothing ) {
                object.traverse( child => {
                    if ( child.geometry ) {
                        child.geometry = toIndexed( child.geometry );
                        child.geometry.computeVertexNormals();
                    }
                } )
            }
        },
        load () {

            if ( !this.src ) return;

            if ( this.object ) {
                this.scene.remove( this.object );
            }

            const onLoad = object => {

                if ( this.process ) {
                    this.process ( object );
                }

                this.object = object;

                this.scene.add( this.object );

                this.updateCamera();

                this.$emit( 'on-load' );

            }

            const onError = err => {

                this.$emit( 'on-error', err );

            }

            if ( this.mtl ) {

                let mtlPath = this.mtlPath;
                let mtlSrc = this.mtl;

                if ( !this.mtlPath ) {

                    const result = /^(.*\/)([^/]*)$/.exec( this.mtl );

                    if ( result ) {
                        mtlPath = result[ 1 ];
                        mtlSrc = result[ 2 ];
                    }

                }

                if ( mtlPath ) {
                    this.mtlLoader.setPath( mtlPath )
                }

                this.mtlLoader.load( mtlSrc, materials => {

                    materials.preload();

                    this.loader.setMaterials( materials );

                    this.loader.load( this.src, onLoad, onError );

                }, onError );

            } else {

                this.loader.load( this.src, onLoad, onError );

            }

        },
    }
}
</script>
