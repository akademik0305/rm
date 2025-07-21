<script lang="ts" setup>
//===============================-< imports >-===============================
import Service from "~/service/Service";
import urls from "~/service/urls";
import type { TCategory } from "~/types/api.types";
const { locale } = useI18n();
const route = useRoute();
const token = useToken();
//===============================-< get category >-===============================
//> variables
const category = ref<TCategory>();
const current_page = ref(1);
//> functions
async function getCategory() {
	const res = await Service.get<TCategory>(
		urls.getCategoryProducts(Number(route.params.id), current_page.value),
		locale.value,
		token.value
	);

	category.value = res.data;
}

getCategory();

//===============================-< change page >-===============================
//> variables
//> functions
function changePage(page: number) {
	current_page.value = page;
	getCategory();
}
</script>
<template>
	<main v-if="category" class="py-6">
		<nav>
			<div class="container">
				<h2 class="text-2xl font-semibold">{{ category.category.name }}</h2>
				<BaseBreadcump
					:links="[
						{ label: $t('home_page'), url: '/' },
						{ label: $t('categories'), url: '/categories' },
						{ label: category.category.name },
					]"
				/>
			</div>
		</nav>
		<section class="mt-8">
			<div class="container">
				<div v-if="category.dataProvider.items.length">
					<div
						class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-3 md:gap-5"
					>
						<ProductCard
							v-for="product in category.dataProvider.items"
							:key="product.id"
							:product="product"
							@success-wishlist="getCategory"
						/>
					</div>
					<div class="mt-5">
						<BasePagination
							v-if="category?.dataProvider?._meta.pageCount > 1"
							:meta="category?.dataProvider?._meta"
							@change-page="changePage"
						/>
					</div>
				</div>
				<div
					v-else
					class="w-full h-80 flex flex-col items-center justify-center gap-2"
				>
					<div class="w-40 h-auto flex justify-center">
						<Icon
							name="icon-park-solid:database-network"
							class="mt-2 text-7xl text-slate-400"
						/>
					</div>
					<p class="font-meduim text-base text-gray-4">
						{{ $t("empty_data_product") }}
					</p>
				</div>
			</div>
		</section>
	</main>
</template>
