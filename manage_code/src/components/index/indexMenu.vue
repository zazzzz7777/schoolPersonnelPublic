<template>
	<div>
		<el-scrollbar wrap-class="scrollbar-wrapper" class="menu_scrollbar">
			<el-menu :default-openeds="[]" :unique-opened="false" default-active="0" class="menu_view"
				:collapse="collapse">
				<el-sub-menu :index="0" @click="menuHandler('')">
					<template #title>
						<i class="iconfont icon-zhuye2" v-if="collapse?true:true"></i>
						<span>首页</span>
					</template>
				</el-sub-menu>
				<el-sub-menu v-for=" (menu,index) in menuList.backMenu" :key="menu.menu" :index="index+2+''">
					<template #title>
						<i class="iconfont" :class="menu.fontClass" v-if="collapse?true:true"></i>
						<span>{{ menu.menu }}</span>
					</template>
					<el-menu-item class="menu_item_view" v-for=" (child,sort) in menu.child" :key="sort"
						:index="(index+2)+'-'+sort" @click="menuHandler(child.tableName,child.menuJump)">{{ child.menu }}
					</el-menu-item>
				</el-sub-menu>
			</el-menu>
		</el-scrollbar>
	</div>
</template>

<script setup>
	import menu from '@/utils/menu'
	import {
		ref,
		toRefs,
		getCurrentInstance,
		nextTick
	} from 'vue';
	const context = getCurrentInstance()?.appContext.config.globalProperties;
	//props
	const props = defineProps({
		collapse: Boolean
	})
	const {
		collapse
	} = toRefs(props)
	//data
	const menuList = ref([])
	const role = ref('')
	const styleChange = () => {
		nextTick(() => {
			document.querySelectorAll('.el-menu-vertical-demo .el-sub-menu .el-menu').forEach(el => {
				el.removeAttribute('style')
				const icon = {
					"border": "none",
					"padding": "0",
					"margin": "10px auto 0",
					"borderRadius": "0px",
					"background": "none",
					"display": "none",
					"width": "100%"
				}
				Object.keys(icon).forEach((key) => {
					el.style[key] = icon[key]
				})
			})
		})
	}
	//权限验证
	const btnAuth = (e,a)=>{
		return context?.$toolUtil.isAuth(e,a)
	}
	const init = () => {
		const menus = menu.list()
		if (menus) {
			menuList.value = menus
		}
		role.value = context?.$toolUtil.storageGet('role')

		for (let i = 0; i < menuList.value.length; i++) {
			if (menuList.value[i].roleName == role.value) {
				menuList.value = menuList.value[i];
				break;
			}
		}
		// styleChange()
	}
	const menuHandler = (name,menuJump) => {
		if(name == 'center'){
			name = `${role.value}Center`
		}
		if(name == 'storeup'){
			name = `storeup?type=${menuJump}`
		}
		if(name == 'exampaper' && menuJump == '12'){
			name = 'exampaperlist'
		}
		if(name == 'examrecord' && menuJump == '22'){
			name = 'examfailrecord'
		}
		let router = context?.$router
		name = '/' + name
		router.push(name)
	}
	init()
</script>

<style lang="scss" scoped>
	// 总盒子
	:deep(.menu_scrollbar) {

		// 菜单盒子-展开样式
		.menu_view {
			border: 0;
			padding: 40px 0 60px;
			color: #fff;
			background: none;
			height: 100%;

			// 无二级菜单
			.el-menu-item {
				border: 0;
				padding: 0 10px;
				color: #c0c4c8;
				background: none;
				border-left: 4px solid #23262d;
				line-height: 50px;
				height: 50px;
				.iconfont {
					margin: 0 5px 0 0;
					color: inherit;
					width: 34px;
					vertical-align: middle;
					font-size: 20px;
					text-align: center;
				}
			}

			// 无二级悬浮
			.el-menu-item:hover {
				padding: 0 10px;
				color: #c0c4c8;
				background: rgba(33,33,33,0.7);
				border-left: 4px solid #e77512;
				line-height: 50px;
				height: 50px;
			}

			// 无二级选中
			.el-menu-item.is-active {
				padding: 0 10px;
				color: #c0c4c8;
				background: rgba(33,33,33,0.7);
				border-left: 4px solid #e77512;
				line-height: 50px;
				height: 50px;
			}

			// 有二级盒子
			.el-sub-menu {
				cursor: pointer;
				border: 0;
				padding: 0 0;
				color: #333;
				white-space: nowrap;
				background: none;
				position: relative;

				// 有二级item
				.el-sub-menu__title {
					border: 0;
					padding: 0 10px;
					color: #c0c4c8;
					background: none;
					border-left: 4px solid #23262d;
					line-height: 50px;
					height: 50px;
					.iconfont {
						margin: 0 5px 0 0;
						color: inherit;
						width: 34px;
						vertical-align: middle;
						font-size: 20px;
						text-align: center;
					}
					.el-sub-menu__icon-arrow{
						margin: -3px 0 0 8px;
						color: inherit;
						vertical-align: middle;
						font-size: 12px;
						position: static;
					}
				}

				// 有二级item悬浮
				.el-sub-menu__title:hover {
					padding: 0 10px;
					color: #c0c4c8;
					background: rgba(33,33,33,0.7);
					border-left: 4px solid #e77512;
					line-height: 50px;
					height: 50px;
				}
			}
			//二级选中
			.is-active {
				.el-sub-menu__title {
					padding: 0 10px;
					color: #c0c4c8;
					background: rgba(33,33,33,0.7);
					border-left: 4px solid #e77512;
					line-height: 50px;
					height: 50px;
				}
			}
			// 二级盒子
			.el-menu--inline {
				border: none;
				padding: 0px;
				background: none;
				// 二级菜单
				.menu_item_view {
					padding: 0 20px 0 52px;
					color: #616161;
					background: none;
					border-left: 4px solid #23262d;
					line-height: 40px;
					height: 40px;
				}
				// 二级悬浮
				.menu_item_view:hover {
					padding: 0 20px 0 52px;
					color: #616161;
					background: none;
					border-left: 4px solid #e77512;
					line-height: 40px;
					height: 40px;
				}
				// 二级选中
				.is-active.menu_item_view {
					padding: 0 20px 0 52px;
					color: #616161;
					background: none;
					border-left: 4px solid #e77512;
					line-height: 40px;
					height: 40px;
				}
			}
		}
		// 菜单盒子-关闭样式
		.el-menu--collapse {
			border: 0;
			padding: 40px 0 30px;
			color: #fff;
			background: none;
			height: 100%;

			// 无二级菜单
			.el-menu-item {
				padding: 0 10px;
				color: #c0c4c8;
				background: none;
				border-left: 4px solid #23262d;
				line-height: 50px;
				height: 50px;
				.iconfont {
					margin: 0 5px 0 0;
					color: inherit;
					width: 34px;
					vertical-align: middle;
					font-size: 20px;
					text-align: center;
				}
			}

			// 无二级悬浮
			.el-menu-item:hover {
				padding: 0 10px;
				color: #c0c4c8;
				background: rgba(33,33,33,0.7);
				border-left: 4px solid #e77512;
				line-height: 50px;
				height: 50px;
			}

			// 无二级选中
			.el-menu-item.is-active {
				padding: 0 10px;
				color: #c0c4c8;
				background: rgba(33,33,33,0.7);
				border-left: 4px solid #e77512;
				line-height: 50px;
				height: 50px;
			}

			// 有二级盒子
			.el-sub-menu {
				cursor: pointer;
				padding: 0;
				color: #333;
				white-space: nowrap;
				background: none;
				position: relative;

				// 有二级item
				.el-sub-menu__title {
					padding: 0 10px;
					color: #c0c4c8;
					background: none;
					border-left: 4px solid #23262d;
					line-height: 50px;
					height: 50px;
					.iconfont {
						margin: 0 5px 0 0;
						color: inherit;
						width: 34px;
						vertical-align: middle;
						font-size: 20px;
						text-align: center;
					}
					.el-sub-menu__icon-arrow{
						margin: -3px 0 0 8px;
						color: inherit;
						vertical-align: middle;
						font-size: 12px;
						position: static;
					}
				}

				// 有二级item悬浮
				.el-sub-menu__title:hover {
					padding: 0 10px;
					color: #c0c4c8;
					background: rgba(33,33,33,0.7);
					border-left: 4px solid #e77512;
					line-height: 50px;
					height: 50px;
				}
			}
			//二级选中
			.is-active {
				.el-sub-menu__title {
					padding: 0 10px;
					color: #c0c4c8;
					background: rgba(33,33,33,0.7);
					border-left: 4px solid #e77512;
					line-height: 50px;
					height: 50px;
				}
			}
			// 二级盒子
			.el-menu--inline {
				border: 0;
				border-radius: 0;
				padding: 0px;
				background: #23262d;
				// 二级菜单
				.menu_item_view {
					border: 0;
					border-radius: 0;
					padding: 0 40px;
					color: #c0c4c8;
					background: none;
					line-height: 40px;
					height: 40px;
				}
				// 二级悬浮
				.menu_item_view:hover {
					border: 0;
					border-radius: 0;
					padding: 0 40px;
					color: #c0c4c8;
					background: none;
					line-height: 40px;
					height: 40px;
				}
				// 二级选中
				.is-active.menu_item_view {
					border: 0;
					border-radius: 0;
					padding: 0 40px;
					color: #c0c4c8;
					background: none;
					line-height: 40px;
					height: 40px;
				}
			}
		}
	}
</style>
<style lang="scss">
	.el-popper{
		.el-menu--popup-container {
			.el-menu--popup{
				border: 0;
				border-radius: 0;
				padding: 0px;
				background: #23262d;
				// 二级菜单
				.menu_item_view {
					border: 0;
					border-radius: 0;
					padding: 0 40px;
					color: #c0c4c8;
					background: none;
					line-height: 40px;
					height: 40px;
				}
				// 二级悬浮
				.menu_item_view:hover {
					border: 0;
					border-radius: 0;
					padding: 0 40px;
					color: #c0c4c8;
					background: none;
					line-height: 40px;
					height: 40px;
				}
				// 二级选中
				.is-active.menu_item_view {
					border: 0;
					border-radius: 0;
					padding: 0 40px;
					color: #c0c4c8;
					background: none;
					line-height: 40px;
					height: 40px;
				}
			}
		}
	}
</style>
