<template>
    <b-container fluid>
        <b-row id="render-container" ref="twoContainer">
        </b-row>
        <b-row id="buttons-row">
            <b-button-group>
                <b-button>Create Zone</b-button>
                <b-button>Remove Zone</b-button>
                <b-button>Assign</b-button>
            </b-button-group>
        </b-row>
    </b-container>
</template>

<script>
    import Two from 'two.js';
    export default {
        name: 'MyceliumUi',
        mounted() {
            // Create a new Two.js instance
            this.two = new Two({
                type: Two.Types.webgl,
                fitted: true,
                autostart: true
            });

            // Append the Two.js drawing to the DOM
            this.two.appendTo(this.$refs.twoContainer);

            // Create a spinning square
            this.square = this.two.makeRectangle(this.two.width / 2, this.two.height / 2, 100, 100);
            this.square.fill = '#3498db'; // Set fill color
            this.square.noStroke();

            // Update the rendering
            this.two.update();

            this.animate();
        },
        methods: {
            animate() {
                // Rotate the square
                this.square.rotation += 0.02;

                // Update the rendering
                this.two.update();

                // Request the next animation frame
                requestAnimationFrame(this.animate);
            },
        },
        beforeDestroy() {
            // Remove the Two.js instance and clean up resources
            if (this.two) {
                this.two.clear();
            }
        },
    };
</script>

<style>
    #render-container {
        height: 90vh; /* 90% of the viewport height */
        background-color: #f0f0f0; /* Set a background color for the canvas */
    }

    /* Custom styles for the buttons row */
    #buttons-row {
      height: 10vh; /* 10% of the viewport height */
    }
</style>
