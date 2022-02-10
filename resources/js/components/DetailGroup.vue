<template>
    <div :class="componentStyle">
        <div :class="titleStyle" class="flex justify-between" v-if="group.title">
            <div>
                <span class="block float-left border-r border-40 pr-4 mr-4"><!--
                --><span class="text-60 text-xs">#</span><!--
                --><span class="text-80">{{index+1}}</span>
                </span>
                <span class="font-bold">{{group.title}}</span>
            </div>
            <span class="translate-button"><a href="#" @click.prevent="translate();" class="text-primary">Send to translation</a></span>
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
        translate() {
            console.log(this.group);
            console.log(this.resource);
            if (confirm('Are you sure you want to send this to translation? Please make sure the text is in english')) {
                axios.get('/custom-nova-actions/translate/partial', {
                    params: {
                        id: this.resourceId, 
                        type: "App\\Models\\Story", 
                        content_id: this.group.key
                    }
                });
            }
        }
    }
}
</script>
