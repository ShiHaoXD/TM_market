<template>
	<div id="app">
		<div
			style="background-color:#6abfde;width:100%;text-align:center;height:100px"
		>
			<img
				src="https://img.alicdn.com/imgextra/i2/O1CN01BpgR4N1GNQIvK5uTi_!!6000000000610-0-tps-1190-110.jpg"
				style="height:100%"
			/>
		</div>
		<topItem />
		<headerlayout />
		<Stickyheader
			:shopCarValue="shopCarValue"
			:reduce="reduce"
			:plus="plus"
			:shanchu="shanchu"
			:showBorder="showBorder"
			style="position:sticky;top:0px;z-index:1000"
		/>
		<centerContent :centerContentValue="centerContentValue" />
		<recommendItem
			v-for="(i, index) in recomdValue"
			:key="index + 'recomd'"
			:itemValue="i"
			:addGoodsInfo="addGoodsInfo"
		/>

		<shortcut />
		<div class="separator"></div>
		<bottomItem />
		<div style="width:100%;height:200px;background-color:#c40000"></div>
		<transition name="liftItem">
			<lift
				:topDistance="topDistance"
				:flitrecmod="flitrecmod(recomdValue)"
				v-if="liftShow"
			/>
		</transition>
	</div>
</template>

<script>
import Headerlayout from "./components/headerLayout.vue";
import topItem from "./components/topItem.vue";
import centerContent from "./components/centerContent.vue";
import recommendItem from "./components/recommendItem.vue";
import shortcut from "./components/shortcut.vue";
import bottomItem from "./components/bottomItem.vue";
import Stickyheader from "./components/Stickyheader.vue";
import lift from "./components/lift.vue";
import axios from "axios";
export default {
	name: "App",
	components: {
		Headerlayout,
		topItem,
		centerContent,
		recommendItem,
		shortcut,
		bottomItem,
		Stickyheader,
		lift,
	},
	methods: {
		addGoodsInfo(value) {
			this.shopCarValue.push({
				imgsrc: value.srcimg,
				goodsnumber: 1,
				goodsprice: value.price,
				kg: value.kg,
				id: "goods",
			});
		},
		reduce(order) {
			this.shopCarValue[order].goodsnumber--;
			if (this.shopCarValue[order].goodsnumber === 0) {
				this.shanchu(order);
			}
		},
		plus(order) {
			this.shopCarValue[order].goodsnumber++;
		},
		shanchu(order) {
			this.shopCarValue.splice(order, 1);
		},
		handleScroll() {
			this.topDistance = document.documentElement.scrollTop;
			console.log(this.topDistance);
		},
		flitrecmod(val) {
			let arr = [];
			for (let i = 0; i < val.length; i++) {
				arr.push({
					title: val[i].title,
					id: val[i].id,
				});
			}
			return arr;
		},
	},
	data() {
		return {
			centerContentValue: {},
			recomdValue: [],
			shopCarValue: [],
			topDistance: 0,
			showBorder: "",
			liftShow: false,
		};
	},
	watch: {
		topDistance(val) {
			if (val >= 196) {
				this.showBorder = "border-bottom:1px solid black;";
			} else {
				this.showBorder = "";
			}
			if (val > 800) {
				this.liftShow = true;
			} else {
				this.liftShow = false;
			}
		},
	},
	created() {
		axios("http://localhost:8080/centerContentValue.json").then((res) => {
			this.centerContentValue = res.data;
		});
		axios("http://localhost:8080/recomdValue.json").then((res) => {
			this.recomdValue = res.data;
		});
	},
	mounted() {
		window.addEventListener("scroll", this.handleScroll);
	},
	destroyed() {
		window.removeEventListener("scroll", this.handleScroll);
	},
};
</script>

<style>
* {
	margin: 0;
	padding: 0;
}
.separator {
	width: 100%;
	height: 0.8px;
	background-color: #d4d9de;
	margin-top: 10px;
}
.liftItem-enter-active {
	animation: liftEnter 0.2s ease-in;
}
.liftItem-leave-active {
	animation: liftLeave 0.2s ease-in;
}
@keyframes liftEnter {
	from {
		transform: scale(0);
	}
	to {
		transform: scale(1);
	}
}
@keyframes liftLeave {
	from {
		transform: scale(1);
	}
	to {
		transform: scale(0);
	}
}
</style>
