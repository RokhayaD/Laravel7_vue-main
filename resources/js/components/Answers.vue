<template>
  <div>
      <div class="row mt-4" v-cloak v-if="count">
          <div class="col-md-12">
              <div class="card">
                  <div class="card-body">
                      <div class="card-title">
                          <h2>{{ title }}</h2>
                      </div>
                      <hr>
                      <answer @deleted="remove(index)" v-for="(answer ,index) in answers" :answer="answer" :key="answer.id" />
                      <div class="text-center mt-3" v-if="nextUrl">
                          <button @click.prevent="fetch(nextUrl)" class="btn btn-outline-secondary">Charger plus de reponse</button>
                      </div>
                  </div>
              </div>
          </div>
      </div>
      <new-answer @created="add" :question-id="question.id" />
  </div>
</template>

<script>
import  answer from './Answer.vue';
import NewAnswer from './NewAnswer';

export default {
    props: ['question'],
    data () {
        return {
         questionId: this.question.id,
         count: this.question.answers_count,
         answers: [],
         nextUrl: null
        }
    },
    created () {
        this.fetch(`/questions/${this.questionId}/answers`);
    },
    methods: {
        fetch(endPoint)
        {
            axios.get(endPoint)
                .then( ({data}) => {
                    this.answers.push(... data.data);
                    this.nextUrl =data.next_page_url
                });
        },
        remove (index){
            this.answers.splice(index ,1);
            this.count--;
        },
        add (answer) {
            this.answers.push(answer);
            this.count++;
        }
    },
    computed: {
        title () {
            return this.count + " " + (this.count > 1 ? 'Answers' : 'Answer');
        }
    },
    components: {
        answer,
        NewAnswer,
    }
}
</script>
