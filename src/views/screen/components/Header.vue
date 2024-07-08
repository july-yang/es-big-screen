<template>
	<div class="es-screen-header">
		<div class="es-csreen-header-left">
			<h2 class="es-screen-logo">
				<span>社区治理智能化支撑服务平台</span>
			</h2>
			<el-dropdown>
				<span class="el-dropdown-link">
					{{ selectedCommunity }}
				</span>
				<template #dropdown>
					<el-dropdown-menu>
						<el-dropdown-item v-for="item in communityList" :key="item.id" @click="handleCommunityChange(item)">{{ item.plot }}</el-dropdown-item>
					</el-dropdown-menu>
				</template>
			</el-dropdown>
		</div>
		<div class="es-screen-header-center">
			<el-tabs v-model="activeName" class="demo-tabs" @tab-click="handleClick">
				<el-tab-pane v-for="tab in tabOptions" :key="tab.value" :label="tab.label" :name="tab.value"></el-tab-pane>
			</el-tabs>
		</div>
		<!-- <div class="es-screen-header-title">{{ store.title }}</div> -->
		<div class="es-screen-header-right">
			<el-icon :size="16">
				<UserFilled />
			</el-icon>
			<span class="es-screen-header-right-text">管理员 admin</span>
			<span>后台管理</span>
			<el-icon :size="16" class="es-screen-header-right-icon">
				<SwitchButton />
			</el-icon>
		</div>
	</div>
</template>

<script setup lang='ts'>
import { computed, onBeforeUnmount, ref } from 'vue'
import dayjs from 'dayjs'
import { useScreenStore } from '@/store'
import darkIcon from '@/assets/images/screen/qiehuan_dark.png'
import lightIcon from '@/assets/images/screen/qiehuan_light.png'
import githubIconDark from '@/assets/images/screen/github_dark.svg'
import githubIconLight from '@/assets/images/screen/github_light.svg'
import { communityList, tabOptions } from './center/amap.js'

const store = useScreenStore()

const icon = computed(() => store.theme === 'dark' ? darkIcon : lightIcon)
const githubIcon = computed(() => store.theme === 'dark' ? githubIconDark : githubIconLight)

const currentTime = ref('')
const timeId = ref()
const selectedCommunity = ref(communityList.length > 0 ? communityList[0].plot : '')
const activeName = ref(1)


function handleChangeTheme() {
	store.$patch({
		theme: store.theme === 'dark' ? 'light' : 'dark'
	})
}

function startTime() {
	timeId.value = setTimeout(() => {
		currentTime.value = dayjs().format('YYYY-MM-DD HH:mm:ss')
		startTime()
	}, 1000)
}

function handleCommunityChange(item) {
	console.log('selectedCommunity', selectedCommunity)
    selectedCommunity.value = item.plot
}

const handleClick = (tab: TabsPaneContext, event: Event) => {
  console.log(tab, event)
}

onBeforeUnmount(() => {
	clearTimeout(timeId.value)
})

startTime()
</script>

<style lang='scss'>
.es-screen-header {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 10px;
	// position: relative;
	width: 100%;
	height: var(--es-header-height);
	// background-image: url('@/assets/images/screen/header_border_dark.png');
	background-size: 100% 100%;
	background-repeat: no-repeat;
	animation: fade 3s;
	&-title {
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translate(-50%, -50%);
		width: 487px;
		height: var(--es-header-height);
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 30px;
		font-weight: 500;
		letter-spacing: 7px;
		text-shadow: 0px 2px 20px rgba(222,171,155,0.6);
	}
	.es-csreen-header-left {
		display: flex;
		align-items: center;
	}
	.es-screen-header-right {
		display: flex;
		align-items: center;
		font-size: 14px;
		.es-screen-header-right-text {
			margin-right: 16px;
		}
		.es-screen-header-right-icon {
			margin-left: 4px;
			cursor: pointer;
		}
	}
	.es-screen-logo {
		display: flex;
    	align-items: center;
		height: calc(var(--es-header-height) - 20px);
	}
	.el-dropdown-link {
		color: lightblue;
		margin-left: 10px;
		cursor: pointer;
		font-size: 22px;
	}
	.el-tabs__item {
      color: #fff !important; /* Change default color to white */
      background-color: transparent; /* Remove default background color */
      border-bottom: none; /* Remove default underline */
	  text-decoration: none !important;
      
      &.is-active {
        color: yellow !important; /* Change selected color to yellow */
      }
	}
	.el-tabs__active-bar {
		background: yellow !important;
	}
}

.el-icon {
  font-size: 24px; /* 设置图标大小 */
  color: red;     /* 设置图标颜色 */
}

@keyframes fade {
	from {
		opacity: 0;
	}
	to {
		opacity: 1;
	}
}
</style>
