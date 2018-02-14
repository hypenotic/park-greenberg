<template>
	<div v-if="loading==false">
		<section class="section" v-if="data && data.hasOwnProperty('meta_box')">
			<div class="container">
				<h1 v-html="data.meta_box._page_grant_heading"></h1>
				
				<div class="topContent" v-html="data.content.rendered"></div>

				<!-- <section class="application-eligibility">
					<div class="application">
						<h2>Application Process</h2>
						<ol class="app-list">
							<li v-for="point in data.meta_box._page_app_process" :key="point['_page_application_copy']" v-html="point['_page_application_copy']">
							</li>
						</ol>
					</div>
					<div class="eligibility">
						<h2>Eligibility</h2>
						<ul>
							<li v-for="point in data.meta_box._page_eligibility" :key="point['_page_eligibility_copy']">
								<img :src="point['_page_eligibility_img']" alt="">
								<span v-html="point['_page_eligibility_copy']"></span>
							</li>
						</ul>
					</div>
				</section> -->

				<!-- <div class="align-center"><a class="cta_button" :href="data.meta_box._page_grant_cta_link" v-html="data.meta_box._page_grant_cta_text"></a></div> -->
			</div>
		</section>
		<!-- <section class="grant-illustration">
			<div class="main-animation">
				<img src="https://parkpeople.ca/listings/custom/uploads/2018/01/parkparadepeople_paradelayer.gif" alt="Parade animation">
			</div>
			<div class="clouds">
			</div>
		</section> -->
		<section class="more-info">
			<div class="container">
				<div v-html="data.meta_box._page_grant_more_info"></div>
			</div>
		</section>
		<!-- <section class="second-cta">
			<div class="container">
				<div class="align-center"><a class="cta_button" :href="data.meta_box._page_grant_cta_link" v-html="data.meta_box._page_grant_cta_text"></a></div>
			</div>
		</section> -->
		<section class="grants-newsletter">
			<div class="container">
				<p>Want to stay up-to-date on Park People news?</p>
				<a class="button" href="http://parkpeople.us2.list-manage.com/subscribe?u=ba963c8c64482c0ad756245c3&id=efc9b053b8" target="_blank">Get the Park People newsletter!</a>
			</div>
		</section>
		<section class="grant-sponsors">
			<p>Made possible by a great collaboration:</p>
			<ul>
				<li v-for="sponsor in data.meta_box._page_grant_sponsors">
					<img :src="sponsor['_page_g_sponsor_img']" alt="logo">
				</li>
			</ul>
		</section>
	</div>
	<div v-else class="loading-panel">
		<div>
			<img src="https://parkpeople.ca/listings/custom/uploads/2018/01/birdflying_pp_small.gif" alt="">
		</div>
	</div>
</template>

<script>
import axios from 'axios';
// import { mapState } from 'vuex'
export default {
	data() {
		return {
			data: {},
			relatedPosts: [],
			errors: [],
			loading: true
		};
	},
	filters: {
		removeHyphen(value){
			return value.replace("-", ' ');
		},
		capitalizeFirstLetter(value) {
			return value.charAt(0).toUpperCase() + value.slice(1);
		},
		toUppercase(value) {
			return value.toUpperCase();
		},
		readMore(value, length, suffix) {
			if (value.length < length)
			return value;	
			return value.substring(0, length) + suffix;
		},
		stripHTML(value) {
			return value.replace(/(<([^>]+)>)/ig,"");
		},
		toTitleCase(value) {
    		return value.replace(/\w\S*/g, (txt) => {
				return txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase();
			});
		},
	},
	computed: {

    },
	methods: {

	},
	mounted() {

	},
	created() {
		axios.get('https://parkpeople.ca/listings/wp-json/wp/v2/pages/1070?_embed')
		.then(response => {
            console.log(response.data)
			this.data = response.data
			
			this.loading = false

			axios.all([
				axios.get('https://parkpeople.ca/listings/wp-json/wp/v2/case-study/?_embed&categories=133&per_page=20'),
				axios.get('https://parkpeople.ca/listings/wp-json/wp/v2/research/?_embed&categories=133&per_page=20'),
				axios.get('https://parkpeople.ca/listings/wp-json/wp/v2/resource/?_embed&categories=133&per_page=20')
			])
			.then(axios.spread((response, response1, response2) => {
				// console.log(response.data)
				let allPosts  = response.data.concat(response1.data, response2.data);
				// console.log('old', allPosts)
				allPosts.sort(function(a,b){
					return new Date(b.date) - new Date(a.date)
				})	
				// console.log('sorted', allPosts)
				this.relatedPosts = allPosts
			}))
			.catch(e => {
				console.log(e)
				this.errors.push(e)
			})

		})
		.catch(e => {
			console.log(e)
			this.errors.push(e)
		})
	},
};
</script>

<style lang="scss" scoped>

@import '../styles/variables.scss';

.section {
	background-color: #fff;
	@media #{$small-and-down} {
		padding: 24px 1.5rem !important;
	}
}

.card-content {
	padding: 0.8rem 1.5rem;
}

h1 {
    color: $orange;
	font-size: 35px;
	line-height: 1.2;
	font-weight: 700;
	@media #{$large-and-up} {
		font-size: 40px;
		line-height: 1.3;
		font-weight: 700;
		margin-top: 20px;
	}
}

.container {
    max-width: 800px;
    margin: 0 auto;
}

.loading-panel {
	min-height: 100vh;
	background: $white;
	display: flex;
	align-items: center;
	justify-content: center;
}

.wide-container {
    max-width: 1160px;
    margin: 0 auto;
}

.content a h4 {
	color: rgba(30,177,242, 1);
	font-size: 1.2rem;
	line-height: 1.7rem;
	font-weight: 700;
	margin-bottom: 0.3rem;
	margin-top: 0.3rem;
	// line-height: 1.4;
	&:hover {
		color: rgba(30,177,242,0.7);
	}
}

.card .content {
	p {
		margin-bottom: 0.3rem;
	}
}

.activity-list-container,
.activity-list-container span,
.activity-list-container strong {
	font-size: 12px;
	line-height: 1.5;
	color: rgba(0,0,0,0.4) !important;
}

.activity-list-container span {
	display:inline-block;
	margin-right: 5px;
	position: relative;
	&:after {
		content: ',';
		position: absolute;
		bottom: 0;
		right: -2px;
		color: rgba(0,0,0,0.4) !important;
	}
}

.activity-list-container span:last-child {
	margin-right: 0px;
	&:after {
		display: none;
	}
}

.skewed-bg{
  	background: #fff;
 	padding: 100px 0;
	transform: skew(0deg, -5deg);
  	margin-top: -130px;
	position: relative;
	z-index: -1;
}

img {
	object-fit: cover;
}

.content {
	p {
		font-size: 0.8rem;
		line-height: 1.3rem;
	}
	ul {
		list-style-type: none;
		margin: 0 0 10px 0;
		li { 
			color: rgba(0,0,0,0.5);
			display: inline;
			font-weight: 700;
			&:not(:last-child):after {
				content: " | ";
			}
		}
	}
}

.topContent {
    margin: 32px 0;
	margin-bottom: 50px;
    font-size: 1rem;
	@media #{$medium-and-up} {
		font-size: 1.2rem;
    }
    p {
        font-size: 1.2rem;
    }
}

.application-eligibility {
	position: relative;
	z-index: 500;
	@media #{$small-and-down} {
        div + div {
			margin-top: 40px;
		}
	}
	@media #{$large-and-up} {
        display: flex;
		justify-content: space-between;
		>div {
			width: 45%;
			// margin: 0 1%;
			h2 {
				font-size: 1rem;
				font-weight: 700;
				font-family: $family-sanserif;
				@media #{$medium-and-up} {
					font-size: 1.2rem;
				}
			}
		}
	}
}

.application-eligibility ul {
	li {
		display: flex;
		align-items: center;
		margin-bottom: 16px;
		font-size: 0.9rem;
		line-height: 1.5;
		img {
			margin-right: 16px;
			max-height: 40px;
			width: auto;
		}
	}
}

.application-eligibility ol {
	list-style: none;
	>li {
		counter-increment: item;
		margin-bottom: 5px;
		position: relative;
		padding-left: 70px;
		padding-bottom: 24px;
		margin-bottom: 8px;
		font-size: 0.9rem;
		line-height: 1.5;
	}
	> li:first-child {
		margin-top: 24px;
	}
	>li:not(:last-child) {
		// border-bottom: 2px dashed $orange;
	}
	>li:before {
		margin-right: 10px;
		content: counter(item);
		background: white;
		border-radius: 100%;
		border: 2px solid $orange;
		color: $orange;
		width: 40px;
		height: 40px;
		font-family: serif;
		font-size: 24px;
		font-style: italic;
		font-weight: bold;
		text-align: center;
		padding-top: 0px;
		// display: inline-block;
		position: absolute;
		top: 0;
		left: 0px;
	}
}

.align-center {
	text-align: center;
	position: relative;
	z-index: 200;
}

.cta_button {
	display: inline-block;
	margin: 0 auto;
	color: $white;
	background-color: $orange;
	border:4px solid $orange;
	padding: 16px 40px;
	border-radius: 50px;
	font-size: 32px;
	margin: 32px 0;
	font-weight: bold;
	&:hover {
		background-color: $white;
		color: $orange;
		border:4px solid $orange;
	}
}

.more-info {
	position: relative;
	z-index: 500;
	p {
		font-size: 1.5rem;
	}
}

.grants-newsletter {
	text-align: center;
	font-size: 0.8rem;
	line-height: 1.5;
	margin-top: 40px;
	p {
		font-size: 0.8rem;
		line-height: 1.5;
	}
}

.grant-sponsors {
	padding-top: 100px;
	text-align: center;
	font-size: 0.8rem;
	line-height: 1.5;
	p {
		font-size: 0.8rem;
		line-height: 1.5;
	}
	ul {
		margin: 0 auto;
		li {
			display: inline-block;
			margin: 0 24px;
			img {
				max-width: 100px;
				height: auto;
			}
		}
	}
}

.grants-newsletter {
	.button {
		background: $white;
		border: 2px solid $blue;
		color: $blue;
		border-radius: 50px;
		font-weight: bold;
		padding: 16px 32px;
		height: 50px;
		@media #{$small-and-down} {
			height: auto;
			margin-top: 8px;
			width: 80%;
			white-space: normal;
			display: block;
			margin: 0 auto;
		}
		&:hover {
			background: $blue;
			border: 2px solid $blue;
			color: $white;
		}
	}
}


.related-resources {
	padding: 24px;
	padding-bottom: 180px;
	margin-top: 50px;
	margin-bottom: -50px;
	@media #{$medium-and-up} {
		padding: 50px;
		padding-bottom: 200px;
	}
	h3 {
		color: $green;
		font-size: 40px;
		@media #{$small-and-down} {
			font-size: 32px;
			font-weight: bold;
		}
	}
	a {
        border-bottom: 1px solid $blue;
        &:hover {
            color: $blue;
            font-weight: bold;
        }
    }
}

.related-resources-copy {
	text-align: center;
	margin-bottom: 24px;
}

@-webkit-keyframes slide {
    from { background-position: 0 0; }
    to { background-position: -400px 0; }
}

@keyframes slide {
    from { background-position: 0 0; }
    to { background-position: -200% 0; }
}

.grant-illustration {
	position: relative;
	margin-top: -200px;
	z-index: 100;
	padding-bottom: 15%;
	min-height: 300px;
	@media #{$medium-and-up} {
		min-height: 300px;
	}
	@media #{$large-and-up} {
		min-height: 500px;
	}
	.main-animation {
		position: absolute;
		left: 0;
		top: 130px;
		z-index: 20;
		@media #{$medium-and-up} {
			top: 15%;
			width: 700px;
			left: 50%;
			margin-left: -350px;
			// right: 50%;
		}
		@media #{$large-and-up} {
			top: -15%;
			width: 1300px;
			left: 50%;
			margin-left: -650px;
			// right: 50%;
		}
		@media #{$xlarge-and-up} {
			width: 1300px;
			left: 50%;
			margin-left: -650px;
			// right: 50%;
		}
	}
	.clouds {
		position: absolute;
		background: url('https://parkpeople.ca/listings/custom/uploads/2018/01/park_people_event_greenline_background_long.jpg') repeat 0 0;
		width: 100%;
		height: 500px;
		background-size: cover;
		left: 0;
		top: 0%;
		z-index: 10;
		-webkit-animation: slide 40s linear infinite alternate;
		animation: slide 40s linear infinite alternate;
		@media #{$small-and-down} {
			height: 350px;
		}
		@media #{$medium-and-up} {
			height: 400px;
		}
		@media #{$large-and-up} {
			height: 500px;
		}
		@media #{$xlarge-and-up} {
			// width: 1000px;
			background-size: contain;
		}
	}
}

#bird-anchor {
	position: relative;
	&:before {
		content: url('https://parkpeople.ca/listings/custom/uploads/2018/01/birdflying_pp_small.gif');
		position: absolute;
		width: 50px;
		height: 50px;
		top: 100px;
		left: -120px;
		-moz-transform: scaleX(-1);
        -o-transform: scaleX(-1);
        -webkit-transform: scaleX(-1);
	}
	@media #{$xlarge-and-up} {
        &:before {
			content: url('https://parkpeople.ca/listings/custom/uploads/2018/01/birdflying_pp_small.gif');
			position: absolute;
			width: 50px;
			height: 50px;
			left: -120px;
			top: 200px;
		}
	}
}

.card__activity-list {
	ul {
		li {
			font-size: 0.8rem;
		}
	}
}
</style>