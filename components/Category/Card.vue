<script lang="ts" setup>
const localePath = useLocalePath();

// props
const props = defineProps({
	category: {
		type: Object,
		required: true,
	},
})
</script>
<template>
	<NuxtLink
		:to="localePath(`/categories/${props.category.id}`)"
		class="block rounded-xl overflow-hidden shadow-md border border-border group p-4"
	>
		<div
			class="w-full h-auto flex items-center justify-center overflow-hidden rounded-lg"
		>
			<img
				:src="category.imageUrl"
				:alt="category.name"
				class="w-full h-32 md:h-40 object-cover group-hover:scale-120 transition-transform duration-300"
			/>
		</div>

		<div class="mt-5">
			<h3
				class="font-medium text-text text-base md:text-xl text-center group-hover:text-main transition-colors line-clamp-1"
			>
				{{ category.name }}
			</h3>
		</div>
	</NuxtLink>
</template>
