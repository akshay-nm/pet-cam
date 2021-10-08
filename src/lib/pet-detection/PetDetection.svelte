<!-- Load the coco-ssd model. -->
<script>
	// Load TensorFlow.js. This is required to use coco-ssd model.
	import '@tensorflow/tfjs';
	import * as cocoSsd from '@tensorflow-models/coco-ssd';
	// Grab DOM reference to video element.
	// const video = document.getElementById('webcamVideo');
	// Load the model.

	let videoSource = null;
	let loading = false;
	let initComplete = false;
	let isDogInTheScene = false;
	let predictions;

	const obtainVideoCamera = async () => {
		try {
			loading = true;
			const stream = await navigator.mediaDevices.getUserMedia({
				video: true
			});
			videoSource.srcObject = stream;
			videoSource.play();
			loading = false;
		} catch (error) {
			console.log(error);
		}
	};
	const predictWebcam = () => {
		cocoSsd.load().then((model) => {
			// detect objects in the image frame.
			model.detect(videoSource).then((pred) => {
				//console.log('Predictions: ');
				predictions = pred;
			});
		});
		window.requestAnimationFrame(predictWebcam);
	};

	$: isDogInTheScene = predictions?.filter(
		(prediction) => prediction.class === 'dog' && prediction.score > 0.75
	);
</script>

<!-- svelte-ignore a11y-media-has-caption -->
<video bind:this={videoSource} on:loadeddata={predictWebcam} />
<div>is dog in the scene: {isDogInTheScene ? 'yes' : 'no'}</div>
<div>Predictions:</div>
<pre>{JSON.stringify(predictions, null ,2)}</pre>
<button on:click={obtainVideoCamera}>Start</button>
