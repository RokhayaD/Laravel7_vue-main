<template>
    <a title="Cliquez pour marquer comme question preferee (cliquez a nouveau pour annuler)"
       :class="classes" @click.prevent="toggle">
        <i class="fas fa-star fa-2x"></i>

        <span v-html="count" class="favorite_count"></span>
    </a>
</template>

<script>
export  default {
    props: ['question'],
    data () {
        return {
            isFavorited: this.question.is_favorited,
            count: this.question.favorites_count,
            id: this.question.id,
        }
    },
    computed: {
        classes () {
            return [
                'favorite' , 'mt-2' ,
                ! this.signedIn ? 'off' : (this.isFavorited ? 'favorited' : '')
            ];
        },
        endpoint (){
            return `/questions/${this.id}/favorites`;
        }
    },
    methods: {
        toggle () {
            if(! this.signedIn) {
                this.$toast.warning("Veuillez vous connecter pour mettre cette question en favori","Avertissement",{
                    timeout:3000,
                    position: 'bottomLeft'
                });
                return;
            }
            this.isFavorited ? this.destroty() : this.create();
        },
        destroty (){
            axios.delete(this.endpoint)
            .then(res => {
                this.count--;
                this.isFavorited =false;
            });
        },
        create (){
           axios.post(this.endpoint)
            .then(res => {
                this.count++;
                this.isFavorited =true;
            });

        }
    }
}
</script>
