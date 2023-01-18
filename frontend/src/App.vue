<template>
	<div id="app" :class="{ 'hide-menu': !isMenuVisible || !user }">
		<MyHeader title="Base de Conhecimento" :hideToggle="!user" :hideUserDropdown="!user" />

		<MyMenu v-if="user" />

		<MyLoading v-if="validatingToken" />

		<MyContent v-else />

		<MyFooter />
	</div>
</template>

<script>
import axios from 'axios'
import { baseApiUrl, userKey } from './global';
import { mapState } from 'vuex'
import MyHeader from '@/components/template/MyHeader.vue';
import MyMenu from '@/components/template/MyMenu.vue';
import MyContent from '@/components/template/MyContent.vue';
import MyFooter from '@/components/template/MyFooter.vue';
import MyLoading from '@/components/template/MyLoading.vue'

export default {
	name: "App",
	components: { MyHeader, MyMenu, MyContent, MyFooter, MyLoading },
	computed: mapState(['isMenuVisible', 'user']),
	data: function () {
		return {
			validatingToken: true
		}
	},
	methods: {
		async validateToken() {
			this.validatingToken = true
			const json = localStorage.getItem(userKey)
			const userData = JSON.parse(json)
			this.$store.commit('setUser', null)
			if (!userData) {
				this.validatingToken = false
				this.$router.push({ name: 'auth' })
				return
			}
			const res = await axios.post(`${baseApiUrl}/validateToken`, userData)
			if (res.data) {
				this.$store.commit('setUser', userData)

				if (this.$mq === 'xs' || this.$mq === 'sm') {
					this.$store.commit('toggleMenu', false)
				}
			} else {
				localStorage.removeItem(userKey)
				this.$router.push({ name: 'auth' })
			}
			this.validatingToken = false
		}
	},
	created() {
		this.validateToken()
	}
}
</script>

<style>
* {
	font-family: "Lato", sans-serif;
}

body {
	margin: 0;
}

#app {
	-webkit-font-smoothing: antialiased;
	-moz-osx-smoothing: grayscale;

	height: 100vh;
	display: grid;
	grid-template-rows: 60px 1fr 40px;
	grid-template-columns: 250px 1fr;
	grid-template-areas:
		"header header"
		"menu content"
		"menu footer";
}

#app.hide-menu {
	grid-template-areas:
		"header header"
		"content content"
		"footer footer"
	;
}
</style>