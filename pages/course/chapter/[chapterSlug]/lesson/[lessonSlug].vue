<template>
    <div>
        <p class="mt-0 uppercase font-bold text-slate-400 mb-1">
            Lesson {{ chapter.number }} - {{ lesson.number }}
        </p>
        <h2 class="my-0"> {{ lesson.title }}</h2>
        <div class="flex space-x-4 mt-2 mb-8">
            <NuxtLink 
            v-if="lesson.sourceUrl"
            class="font-normal text-md text-gray-500"
            :to="lesson.sourceUrl">
                Downlooad Source
            </NuxtLink>
            <NuxtLink
            v-if="lesson.downloadUrl"
            class="font-normal text-md text-gray-500"
            :to="lesson.downloadUrl">
                Download Video
            </NuxtLink>
        </div>
        <VideoPlayer 
        if="lesson.videoId"
        :videoId="lesson.videoId" />
        <p> {{ lesson.text }}</p>
            <LessonCompleteButton 
            :modelValue="isLessonComplete"
            @update:modelValue="toggleComplete"
            />
    </div>
</template>

<script setup>
const course = useCourse()
const route = useRoute()

const chapter = computed(()=> {
    return course.chapters.find(chapter => chapter.slug == route.params.chapterSlug)
})

const lesson = computed(() =>{
    return chapter.value.lessons.find(lesson=> lesson.slug == route.params.lessonSlug)
})

const title = computed(()=> `${lesson.value.title} - ${course.title}`);
useHead({
    title
})


const progress = useLocalStorage('progress',[])

const isLessonComplete = computed(()=> {
    // periksa apakah ada array pada chapter tersebut
    if (!progress.value[chapter.value.number - 1]) {
        return false;
    }
    // periksa apakah ada array leson pada chapter tersebut
    if (!progress.value[chapter.value.number - 1][lesson.value.number - 1]) {
        return false
    }
    return progress.value[chapter.value.number - 1][lesson.value.number - 1]
})

const toggleComplete = () => {
    if (!progress.value[chapter.value.number - 1]) {
        progress.value[chapter.value.number - 1] = []
    }
    progress.value[chapter.value.number - 1][lesson.value.number - 1] = !isLessonComplete.value;
}

</script> 