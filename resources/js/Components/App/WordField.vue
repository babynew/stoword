<template>
    <div class="max-w-2xl mx-auto">
        <form
            @submit.prevent="onSubmit"
            class="p-5 bg-gray-200 dark:bg-gray-800 rounded-md shadow"
        >
            <div class="flex justify-between items-center">
                <TextInput
                    v-model="form.prompt"
                    type="text"
                    placeholder="Type a word or an expression ..."
                    class="w-full h-16 text-2xl rounded-lg mr-3"
                    :disabled="loading"
                />
                <PrimaryButton
                    :disabled="loading || !form.prompt || form.prompt.length > 20"
                    type="submit"
                    class="h-16 text-md rounded-lg"
                >
                    <PaperAirplane />
                </PrimaryButton>
            </div>
            <ExtraLinks
                v-if="wordResponse.word"
                :word-sent="wordResponse.word"
                :native-lang="wordResponse.native_language"
            />
        </form>
        <Loading v-if="loading" />
        <InputError class="p-2 mt-2" :message="error" />
        <WordInfo v-if="wordResponse.word" />
        <WordMean v-if="wordResponse.meaning?.value" />
        <Sentences v-if="wordResponse.sentences?.length" />
    </div>
</template>

<script setup>
import { useForm } from "@inertiajs/vue3";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import TextInput from "@/Components/TextInput.vue";
import { useFetch } from "@/Composables/useFetch";
import Loading from "./Loading.vue";
import Sentences from "./Sentences.vue";
import InputError from "../InputError.vue";
import { computed } from "vue";
import { useStore } from "vuex";
import ExtraLinks from "./ExtraLinks.vue";
import WordInfo from "./WordInfo.vue";
import WordMean from "./WordMean.vue";
import PaperAirplane from "@/Components/Icons/PaperAirplane.vue";

const store = useStore();

const form = useForm({
    prompt: "",
});

const { loading, error, getData } = useFetch();

const wordResponse = computed(() => store.state.wordResponse);

const onSubmit = async () => {
    await getData(
        route("generate.sentences", {
            prompt: form.prompt
        })
    );

    if (!error.value) {
        form.reset();
    }
};
</script>
