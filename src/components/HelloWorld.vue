<template>
	<div class="scroll_wrap">
		<div class="scroll">
			<div class="media" v-for="list in imageLists">
				<div class="media-left media-middle">
					<a href="#">
						<img class="media-object" :src="list.url" width="100" height="100">
					</a>
				</div>
				<div class="media-body">
					<h4 class="media-heading">{{list.publishedAt}}</h4>
				</div>
			</div>
			<div v-show="noMoreData==true">已经加载完了</div>
		</div>
		
	</div>

</template>
<script>
	export default {
		data() {
			return {
				page: 1,
				imageLists: [],
				noMoreData:false
			}
		},
		created() {
			var data = new URLSearchParams;
			data.append("page", this.page);
			var url = "https://www.apiopen.top/meituApi";
			this.$axios.post(url, data).then(res => {
				this.imageLists = res.data.data;
			})
		},
		mounted() {
			var _this = this;
			var isscroll = true;
			_this.$nextTick(() => {
				var el = document.querySelector(".scroll");
				var offsetHeight = el.offsetHeight;
				el.onscroll = () => {
					var scrollTop = el.scrollTop;
					var scrollHeight = el.scrollHeight;
					//console.log(offsetHeight, scrollTop, scrollHeight);
					if (offsetHeight + scrollTop - scrollHeight >= -1) {
						isscroll = false;
						var requestData=[];
							_this.page++;
							isscroll = false;
							var data = new URLSearchParams;
							data.append("page", _this.page);
							var url = "https://www.apiopen.top/meituApi";
							this.$axios.post(url, data).then(res => {
								requestData = res.data.data;
								if(requestData.length==0){
									_this.noMoreData=true;
									isscroll = true;
									return false;
								}
							})
							setTimeout(()=>{
								_this.imageLists = _this.imageLists.concat(requestData);
								isscroll = true;
							},100
							)
							
					}
				};
			});
		}
	}
</script>
<style>
	html,
	body {
		width: 100%;
		height: 100%;
	}

	.scroll_wrap {
		width: 100%;
		height: 100%;
		position: relative;
	}

	.scroll {
		width: 100%;
		height: 100%;
		position: absolute;
		left: 0;
		top: 0;
		overflow-x: hidden;
		overflow-y: scroll;
		/* padding-bottom: 140px; */
	}
</style>
