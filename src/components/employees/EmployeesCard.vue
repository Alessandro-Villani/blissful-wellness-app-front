<script>
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/';

export default {
    name: 'Employee Card',
    data() {
        return {
            reviewsShow: false,
            addReviewShow: false,
            review: {
                grade: 1,
                review: '',
                therapist: this.therapist.id,
                author: this.userId,
            }
        }
    },
    props: {
        index: Number,
        therapist: Object,
        userId: Number,
        isLogged: Boolean
    },
    emits: ['review-store'],
    methods: {
        toggleReviews() {
            this.addReviewShow = false;
            this.reviewsShow = !this.reviewsShow;
        },
        postReview() {
            axios.post(baseApiUrl + 'reviews/store', this.review)
                .then(() => {
                    this.$emit('review-store');
                    this.addReviewShow = false;
                    const container = this.$refs.reviewContent;
                    console.log(container);
                    setTimeout(() => {
                        container.scrollTo({
                            top: container.scrollHeight,
                            behavior: 'smooth'
                        });
                    }, 300);

                })
                .catch(e => console.log(e))
        }
    }
}
</script>

<template>
    <!-- TOP SVG -->
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 320">
        <path fill="#ffffff" fill-opacity="1"
            d="M0,128L40,149.3C80,171,160,213,240,240C320,267,400,277,480,261.3C560,245,640,203,720,170.7C800,139,880,117,960,117.3C1040,117,1120,139,1200,170.7C1280,203,1360,245,1400,266.7L1440,288L1440,320L1400,320C1360,320,1280,320,1200,320C1120,320,1040,320,960,320C880,320,800,320,720,320C640,320,560,320,480,320C400,320,320,320,240,320C160,320,80,320,40,320L0,320Z">
        </path>
    </svg>

    <!-- CARD -->
    <div class="employee-card mb-0 row align-items-center" :class="index % 2 === 0 ? 'flex-row-reverse' : 'flex-row'">
        <div class="col-5 d-flex flex-column align-items-center justify-content-center">
            <div class="col-12 d-flex flex-column justify-content-between align-items-center">
                <h4 class="mb-3">{{ therapist.therapistName }}</h4>
            </div>
            <img class="employee-pic border rounded-circle img-fluid mb-3" :src="therapist.user.profilePic"
                :alt="therapist.user.username">
            <div class="col-12 d-flex flex-column justify-content-between align-items-center">
                <p class="vote mb-1">Rating: <i v-for="i in 5" :key="i" class="fa-star"
                        :class="i <= therapist.reviewAverage ? 'fa-solid' : 'fa-regular'"></i></p>
                <p class="vote mb-0">({{ therapist.reviews.length ??
                    '0'
                }} Reviews)</p>
            </div>
        </div>
        <div class="col-7 mb-2">
            <p class="description mb-0" :class="index % 2 === 0 ? 'text-end' : 'text-start'">
                {{ therapist.description }}
            </p>
        </div>

        <!-- REVIEWS SECTION -->
        <div class="col-12 reviews d-flex flex-column mt-3">
            <button class="btn-reviews mb-3" @click="toggleReviews"><i class="fa-solid fa-chevron-down"
                    :class="reviewsShow ? 'rotated' : ''"></i> Show
                reviews</button>
            <div v-if="reviewsShow" class="reviews-conteiner">
                <div class="reviews-content border rounded-2 p-2 mb-3" ref="reviewContent">
                    <div v-if="therapist.reviews.length" class="row border-bottom mb-3" v-for="review in therapist.reviews">
                        <div class="col-12 text-center">
                            <p class="mb-1">{{ review.author.username }}</p>
                        </div>
                        <div class="col-12 text-center">
                            <p class="mb-3"><i v-for="i in 5" :key="i" class="fa-star"
                                    :class="i <= review.grade ? 'fa-solid' : 'fa-regular'"></i></p>
                        </div>
                        <div class="col-12">
                            <p class="mb-3 px-3">{{ review.review }}</p>
                        </div>
                    </div>
                    <div class="p-2 text-center" v-else>No reviews yet</div>
                </div>
                <div class="d-flex flex-column text-center">
                    <button v-if="!addReviewShow" class="btn btn-primary align-self-center" :disabled="!isLogged"
                        @click="addReviewShow = true">Add Review</button>

                    <!-- REVIEW FORM -->
                    <form @submit.prevent="postReview" v-if="addReviewShow" class="review-creation d-flex flex-column">
                        <h4 class="mb-3">Insert your review</h4>
                        <p class="mb-3">Rating: <i v-for="i in 5" :key="i" class="fa-star"
                                :class="i <= review.grade ? 'fa-solid' : 'fa-regular'" @click="review.grade = i"></i></p>
                        <label for="review">Review</label>
                        <textarea class="mb-2" name="review" id="review" v-model="review.review"></textarea>
                        <button class="btn btn-success align-self-center">Send</button>
                    </form>
                </div>
            </div>
        </div>
    </div>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 320">
        <path fill="#ffffff" fill-opacity="1"
            d="M0,128L40,149.3C80,171,160,213,240,240C320,267,400,277,480,261.3C560,245,640,203,720,170.7C800,139,880,117,960,117.3C1040,117,1120,139,1200,170.7C1280,203,1360,245,1400,266.7L1440,288L1440,0L1400,0C1360,0,1280,0,1200,0C1120,0,1040,0,960,0C880,0,800,0,720,0C640,0,560,0,480,0C400,0,320,0,240,0C160,0,80,0,40,0L0,0Z">
        </path>
    </svg>
</template>

<style scoped lang="scss">
svg {
    margin-right: calc(-.5 * var(--bs-gutter-x));
    margin-left: calc(-.5 * var(--bs-gutter-x));
}

.employee-card {

    background-color: white;


    .employee-pic {
        height: 120px;
        width: 120px;
        object-fit: cover;
        object-position: top;
    }

    h4 {
        font-size: 20px;
    }

    .description,
    .vote {

        font-size: 12px;
    }

    .reviews {
        .btn-reviews {

            background-color: transparent;
            border: none;
            font-size: 14px;

            .fa-chevron-down {

                transition: all 0.5s;

                &.rotated {

                    transform: rotate(-180deg);

                }

            }

        }

        .reviews-content {

            max-height: 160px;
            overflow-y: auto;
            overflow-x: hidden;
            font-size: 12px;

        }
    }

}
</style>