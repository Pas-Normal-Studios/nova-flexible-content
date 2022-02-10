<template>
    <div :class="componentStyle" :id="group.key">
        <div :class="titleStyle" class="flex justify-between" v-if="group.title">
            <div>
                <span class="block float-left border-r border-40 pr-4 mr-4"><!--
                --><span class="text-60 text-xs">#</span><!--
                --><span class="text-80">{{index+1}}</span>
                </span>
                <span class="font-bold">{{group.title}}</span>
            </div>
            <div>
                <span class="copy-button"><a href="#" @click.prevent="copy('de');" class="text-primary">Copy to German</a></span>
                <span class="copy-button"><a href="#" @click.prevent="copy('jp');" class="text-primary">Copy to Japanese</a></span>
                <span v-if="allowTranslation(group.name)" class="translate-button"><a href="#" @click.prevent="translate();" class="text-primary">Send to translation</a></span>
            </div>
        </div>
        <component
            v-for="(item, index) in group.fields"
            :key="index"
            :is="'detail-' + item.component"
            :resource-name="resourceName"
            :resource-id="resourceId"
            :resource="resource"
            :field="item"
            :errors="validationErrors"
            :class="{ 'remove-bottom-border': index == group.fields.length - 1 }"
        />
    </div>
</template>

<script>
export default {
    props: ['group', 'index', 'last', 'resource', 'resourceName', 'resourceId'],

    computed: {
        componentStyle() {
            return this.last ? [] : ['border-b border-50 pb-4 mb-4'];
        },
        titleStyle() {
            return ['pb-4', 'border-b', 'border-40'];
        }
    },

    methods: {
        allowTranslation(layout) {
            console.log(layout);
            return [
            'text-section',
            'text-section-with-image',
            'story-top-section',
            'bullet-point',
            'hero-module',
            'product-slider-section',
            'collapsible-section',
            'text-section-with-landscape-image'
            ].indexOf(layout) > -1; 
        },
        translate() {
            if (confirm('Are you sure you want to send this to translation? Please make sure the text is in english')) {
                axios.get('/custom-nova-actions/translate/partial', {
                    params: {
                        id: this.resourceId, 
                        type: "App\\Models\\Story", 
                        content_id: this.group.key
                    }
                }).then(() => {
                    alert('Element sent to translation');
                });
            }
        },
        copy(lang) {
            axios.get('/custom-nova-actions/story/copy/partial', {
                params: {
                    id: this.resourceId, 
                    content_id: this.group.key,
                    lang
                }
            }).then(response => {
                let tab;
                if (lang == 'de') {
                    tab = 'german';
                }
                else if (lang == 'jp') {
                    tab = 'japanese';
                }
                document.location.href = '/admin/resources/stories/' + this.resourceId + '?tab=' + tab + '#' + response.data.key;
            });
        }
    }
}
</script>
