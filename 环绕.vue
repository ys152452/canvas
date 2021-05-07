<template >
	<div class="app-canvas">
		<canvas id="canvasBg" width="1200" height="600"></canvas>
		<canvas id="canvas" width="1200" height="600"></canvas>
	</div>
</template>

<script type="text/javascript" >
export default {
	data() {
		return {
			canvasBg: null,
			ctxBg: null,
			canvasPoints: null,
			ctx: null,
			points: [
				{
					text: 'XXX公共资源交易中心1',
				},
				{
					text: 'XXX公共资源交易中心2',
				},
				{
					text: 'XXX公共资源交易中心3',
				},
				{
					text: 'XXX公共资源交易中心4',
				},
				{
					text: 'XXX公共资源交易中心5',
				},
				{
					text: 'XXX公共资源交易中心6',
				},
				{
					text: 'XXX公共资源交易中心7',
				},
				{
					text: 'XXX公共资源交易中心8',
				},
			],
		};
	},
	mounted() {
		this.canvasBg = document.getElementById('canvasBg');
		this.canvasPoints = document.getElementById('canvas');
		this.ctxBg = this.canvasBg.getContext('2d');
		this.ctx = this.canvasPoints.getContext('2d');
		this.canvasPoints.addEventListener('mouseenter', () => {
			clearInterval(this.timer);
		});
		this.canvasPoints.addEventListener('mouseleave', () => {
			clearInterval(this.timer);
			this.timer = setInterval(this.draw, 45);
		});
		this.canvasPoints.addEventListener(
			'mousemove',
			(e) => {
				if (this.checkPointInPath(e)) {
					document.body.style.cursor = 'pointer';
				} else {
					document.body.style.cursor = 'default';
				}
			},
			{
				passive: true,
			}
		);
		this.canvasPoints.addEventListener('mousedown', (e) => {
			if (this.checkPointInPath(e)) {
				console.log(this.checkPointInPath(e));
			}
		});
		this.drawBg();
		this.drawPoints();
		this.timer = setInterval(this.draw, 45);
	},
	beforeDestroy() {},
	methods: {
		drawBg() {
			this.ctxBg.ellipse(600, 400, 400, 200, 0, 0, Math.PI * 2);
			this.ctxBg.fillStyle = 'rgba(64, 169, 255, .2)';
			this.ctxBg.fill();
			this.ctxBg.beginPath();
			this.ctxBg.fillStyle = 'rgba(64, 169, 255, .8)';
			this.ctxBg.arc(600, 380, 120, 0, Math.PI * 2, false);
			this.ctxBg.fill();
			this.ctxBg.font = '24px Arial';
			this.ctxBg.textAlign = 'center';
			this.ctxBg.textBaseline = 'middle';
			this.ctxBg.fillStyle = '#ffffff';
			this.ctxBg.fillText('XX省公共资源交易', 600, 360, 200);
			this.ctxBg.fillText('XXXX公共平台', 600, 400, 200);
		},
		drawPoint(point) {
			if (point.r == 360) {
				point.r = 0;
			}
			this.ctx.beginPath();
			this.ctx.clearRect(
				point.x - point.radius - 2,
				point.y - point.radius - 2,
				(point.radius + 2) * 2,
				(point.radius + 2) * 2
			);
			let radian = point.r * (Math.PI / 180);
			point.x = 600 + 360 * Math.cos(radian);
			point.y = 380 + 160 * Math.sin(radian);
			point.radius = ((point.y - 180) / 380) * 20 + 40;
			let fontSize = ((point.y - 180) / 380) * 6 + 10;
			this.ctx.fillStyle = 'rgba(64, 169, 255, 1)';
			this.ctx.lineWidth = 0;
			this.ctx.arc(point.x, point.y, point.radius, 0, Math.PI * 2, false);
			this.ctx.fill();
			this.ctx.font = fontSize + 'px Arial';
			this.ctx.textAlign = 'center';
			this.ctx.textBaseline = 'middle';
			this.ctx.fillStyle = '#ffffff';
			if (point.text.length > 6) {
				let str1 = point.text.substring(0, 6);
				let str2 = point.text.substring(6);
				this.ctx.fillText(str1, point.x, point.y - 4, point.radius * 2);
				this.ctx.fillText(
					str2,
					point.x,
					point.y + 12,
					point.radius * 2
				);
			} else {
				this.ctx.fillText(
					point.text,
					point.x,
					point.y,
					point.radius * 2
				);
			}
			point.r += 1;
			this.ctx.save();
		},
		draw() {
			window.requestAnimationFrame(() => {
				this.points.forEach((point, index) => {
					this.drawPoint(point);
				});
			});
		},
		checkPointInPath(e) {
			let x = e.clientX - document.getElementsByClassName('app-canvas')[0].offsetLeft;
			let y = e.clientY - document.getElementsByClassName('app-canvas')[0].offsetTop;
			let isPointInPath = null;
			this.points.forEach((point) => {
				this.ctx.beginPath();
				this.ctx.arc(
					point.x,
					point.y,
					point.radius,
					0,
					Math.PI * 2,
					false
				);
				if (this.ctx.isPointInPath(x, y)) {
					isPointInPath = point;
				}
				this.ctx.restore();
			});
			return isPointInPath;
		},
		drawPoints() {
			let count = this.points.length;
			let r = parseInt(360 / count);
			this.points.forEach((point, index) => {
				Object.assign(point, {
					x: 200,
					y: 400,
					r: index * r,
				});
				this.drawPoint(point);
			});
		},
	},
};
</script>

<style type="text/css" lang="less" scoped="scoped">
.app-canvas {
	width: 1200px;
	height: 600px;
	margin: 0 auto;
	position: relative;

	#canvas {
		position: absolute;
		top: 0;
		left: 0;
		z-index: 2;
	}
}
</style>