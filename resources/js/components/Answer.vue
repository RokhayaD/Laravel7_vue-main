<template>
    <div class="media post">
        <vote  :model="answer" name="answer" />
        <div class="media-body">
            <form v-if="editing" @submit.prevent="update">
                <div class="form-group">
                    <textarea class="form-control" v-model="body" rows="10"  required></textarea>
                </div>
                <button  class="btn btn-sm btn-outline-primary" :disabled="isInvalid">Update</button>
                <button @click="cancel" type="button" class="btn btn-sm btn-outline-secondary">Cancel</button>
            </form>
            <div v-else>
                <div v-html="bodyHtml"></div>
                <div class="row">
                    <div class="col-4">
                        <div class="ml-auto">
                            <a v-if="authorize('modify',answer)" @click.prevent="edit" class="btn btn-sm btn-outline-primary">Edit</a>
                            <button v-if="authorize('modify',answer)" @click="destroy" class="btn btn-sm btn-outline-danger">Delete</button>
                        </div>
                    </div>
                    <div class="col-4"></div>
                    <div class="col-4">

                        <user-info :model="answer" label="answered" />
                    </div>
                </div>
            </div>

        </div>
    </div>
</template>
<script>
import Vote from './vote.vue';
import UserInfo from "./UserInfo";

export default {
    props: ['answer'],
    components: {
        Vote,
        UserInfo
    },
    data () {
        return {
            editing: false,
            body: this.answer.body,
            bodyHtml: this.answer.body_html,
            id:this.answer.id,
            questionId:this.answer.question_id,
            beforeEditCache: null

        }
    },
    methods: {
        edit (){
          this.beforeEditCache = this.body;
          this.editing =true;
        },
        cancel () {
            this.body = this.beforeEditCache;
            this.editing =false;
        },

        update () {
            axios.patch(this.endpoint , {
                body: this.body
            })
              .then(res => {
                  console.log(res);
                  this.editing =false;
                  this.bodyHtml = res.data.body_html;
                  this.$toast.success(res.data.message,'Success' , { timeout: 3000 });
              })
              .catch(err => {
                 //$toast from vue-izitoast libary
                  this.$toast.error(err.response.data.message,'Error' , { timeout: 3000 });
              });
        },
        destroy () {
            this.$toast.question('Etes-vous sur de cela?',"Confirmer",{
                timeout: 20000,
                close: false,
                overlay: true,
                displayMode: 'once',
                id: 'question',
                zindex: 999,
                title: 'Hey',
                position: 'center',
                buttons: [
                    ['<button><b>YES</b></button>',  (instance, toast) => {

                            axios.delete(this.endpoint)
                                .then(res => {
                                   this.$emit('deleted')
                                });
                        instance.hide({ transitionOut: 'fadeOut' }, toast, 'button');

                    }, true],
                    ['<button>NO</button>', function (instance, toast) {

                        instance.hide({ transitionOut: 'fadeOut' }, toast, 'button');

                    }],
                ],

            });

        }
    },
    computed: {
        isInvalid (){
            return  this.body.length  < 10;
        },
        endpoint () {
            return `/questions/${this.questionId}/answers/${this.id}`;
        }
    }
}
</script>
