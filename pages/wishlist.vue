<script setup lang="ts">
//===============================-< imports >-===============================
import type { TWishlist } from '~/types/api.types';
import Service from '~/service/Service'
import urls from '~/service/urls'
const { locale } = useI18n()
const token = useToken();
//===============================-< get wishlists >-===============================
//> variables
const wishlists = ref<TWishlist>()
//> functions
async function getWishlists() {
	const res = await Service.get<TWishlist>(urls.getWishlists(), locale.value, token.value)
	wishlists.value = res.data
}

getWishlists()
</script>
<template>
	<main class="py-6">
		<nav>
			<div class="container">
				<h2 class="text-2xl font-semibold">{{ $t('wishlist') }}</h2>
				<BaseBreadcump
					:links="[
						{ label: $t('home_page'), url: '/' },
						{ label: $t('wishlist') },
					]"
				/>
			</div>
		</nav>

		<section class="mt-8">
			<div class="container">
				<div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-3 md:gap-5">
					<ProductCard v-for="product in wishlists" :key="product.id" :product="product" @success-wishlist="getWishlists" />
				</div>
			</div>
		</section>
	</main>
</template>
