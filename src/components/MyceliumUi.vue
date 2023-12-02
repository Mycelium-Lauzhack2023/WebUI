<template>
    <b-container fluid>
        <b-row id="render-container">
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
            // eslint-disable-next-line
            const app = new PIXI.Application({
                background: '#C0B6B4'
            });
            
            // Create a new Two.js instance
            new Two({
                width: 400,
                height: 400,
            });

            document.getElementById('render-container').appendChild(app.view);

            // create a new Sprite from an image path
            // eslint-disable-next-line
            const bunny = PIXI.Sprite.from('https://pixijs.com/assets/bunny.png');

            // center the sprite's anchor point
            bunny.anchor.set(0.5);

            // move the sprite to the center of the screen
            bunny.x = app.screen.width / 2;
            bunny.y = app.screen.height / 2;

            app.stage.addChild(bunny);

            // Listen for animate update
            app.ticker.add((delta) =>
                {
                    // just for fun, let's rotate mr rabbit a little
                    // delta is 1 if running at 100% performance
                    // creates frame-independent transformation
                    bunny.rotation += 0.1 * delta;
                });
        }
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
