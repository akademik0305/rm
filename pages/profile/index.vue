<script setup lang="ts">
//===============================-< imports >-===============================
import * as z from "zod";
// utils
import Service from "~/service/Service";
import urls from "~/service/urls";
import type { TUser } from "~/types/auth.type";
const { locale, t } = useI18n();
const token = useToken();

//===============================-< get profile >-===============================
//> variables
const profile = ref<TUser>();
//> functions
async function getProfileInfo() {
	const res = await Service.get<TUser>(
		urls.profileInfo(),
		locale.value,
		token.value
	);
	profile.value = res.data;

	if (res.status === 200) {
		stateUser.firstname = profile.value.firstname || "";
		stateUser.lastname = profile.value?.lastname || "";
		stateUser.phone = profile.value?.username;
	}
}

getProfileInfo();

//===============================-< submit user data >-===============================
//> variables
const schemaUser = z.object({
	firstname: z
		.string({ required_error: t("enter_name") })
		.min(3, t("wrong_name")),
	lastname: z.string({ required_error: t("enter_lastname") }),
	phone: z
		.string({ required_error: t("enter_phone") })
		.min(17, t("wrong_number")),
});

type SchemaUser = z.output<typeof schemaUser>;
const stateUser = reactive<Partial<SchemaUser>>({
	firstname: "",
	lastname: "",
	phone: "+998",
});

//> functions
async function onSubmitUser() {
	const formdata = new FormData();
	formdata.append("firstname", stateUser.firstname ?? "");
	formdata.append("lastname", stateUser.lastname ?? "");
	const res = await Service.post(
		urls.updateName(),
		locale.value,
		formdata,
		token.value
	);

	if (res.status === 200) {
		getProfileInfo();
	}
}

//===============================-< change phone >-===============================
//> variables
const isOpenChangePhone = ref(false);
const openChangePhone = () => {
	isOpenChangePhone.value = true;
};
const closeChangePhone = () => {
	isOpenChangePhone.value = false;
};

//> functions
function submitChangePhone() {
	closeChangePhone();
}

//===============================-< on page load >-===============================
//> variables
//> functions
onMounted(() => {});
</script>

<template>
	<main class="py-10">
		<div class="container max-w-7xl mx-auto px-4 flex-1 flex flex-col">
			<div
				class="flex items-stretch justify-start gap-5 flex-1 flex-col md:flex-row"
			>
				<!-- sidebar start -->
				<profile-sidebar />
				<!-- sidebar end -->

				<div class="flex gap-4 flex-col xl:flex-row w-full flex-1">
					<div
						class="bg-white border border-border rounded-2xl p-7 flex-1 w-full"
					>
						<h3 class="font-semibold text-xl text-pr-text">
							{{ $t("personal_info") }}
						</h3>

						<UForm :schema="schemaUser" :state="stateUser" class="mt-8 flex-1">
							<div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
								<UFormField name="firstname" :label="$t('firstname')" class="">
									<UInput
										v-model="stateUser.firstname"
										size="xl"
										class="w-full mx-auto flex justify-center"
									/>
								</UFormField>
								<UFormField name="lastname" :label="$t('lastname')" class="">
									<UInput
										v-model="stateUser.lastname"
										size="xl"
										class="w-full mx-auto flex justify-center"
									/>
								</UFormField>
								<button
									type="submit"
									class="flex items-center justify-center gap-2 bg-main border border-bg rounded-xl w-full text-white py-2.5 px-10 group hover:bg-bg hover:border-main hover:text-main transition-colors"
									@click="onSubmitUser"
								>
									{{ $t("save") }}
								</button>
								<button
									type="button"
									class="flex items-center justify-center gap-2 bg-white border border-main rounded-xl w-full text-main py-2.5 px-10 group hover:bg-main hover:border-main hover:text-white transition-colors"
									@click="openChangePhone"
								>
									{{ $t("change_phone") }}
								</button>
							</div>
						</UForm>
					</div>
					<div
						class="bg-main rounded-2xl p-4 flex flex-col items-center justify-center gap-2 w-full xl:w-64"
					>
						<p class="font-medium text-text text-lg">{{ $t("balance") }}</p>
						<p class="font-semibold text-text text-2xl">
							{{ profile?.balanceFormat }}
						</p>
					</div>
				</div>
			</div>
		</div>

		<!--=== modals ===-->
		<!-- send phone number -->

		<BaseModal
			v-if="profile?.username"
			:is-open="isOpenChangePhone"
			@close="closeChangePhone"
		>
			<template #header>
				<div class="">
					<h5 class="font-semibold text-xl text-center">
						{{ $t("change_phone") }}
					</h5>
				</div>
			</template>
			<AuthChangePhone
				:phone="profile?.username"
				@success="submitChangePhone"
			/>
		</BaseModal>
		<!--=== modals ===-->
	</main>
</template>
