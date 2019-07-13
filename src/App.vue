<template>
	<v-app dark>
		<v-content>
			<v-container fluid>
				<v-card>
					<v-toolbar color="secondary" dark>
						<v-toolbar-title>
							SPIZDIT CHAT
						</v-toolbar-title>
					</v-toolbar>
					<v-divider></v-divider>
					<v-card-text>
						<v-layout align-center>
							<v-text-field
								v-model="vidId"
								label="Twitch link"
							></v-text-field>
							<v-btn fab dark small color="primary" @click="nextMsg('content_offset_seconds=0')">
								<v-icon>send</v-icon>
							</v-btn>
						</v-layout>
					</v-card-text>
				</v-card>

				<v-card class="mt-4">
					<v-toolbar color="secondary" dark>
						<v-toolbar-title>
							CHAT HISTORY
						</v-toolbar-title>
					</v-toolbar>
					<v-divider></v-divider>
					<v-card-text>
						<p v-for="msg in messages">
							<b>{{msg.sender}} : </b> <span>{{msg.msg}}</span>
						</p>
					</v-card-text>
				</v-card>
			</v-container>
		</v-content>
	</v-app>
</template>

<script>
	import axios from './axios'
	import _ from 'lodash'

	export default {
		name: 'app',
		components: {},
		data() {
			return {
				vidId: 451763998,
				messages: []
			}
		},
		methods: {
			nextMsg(query) {
				let self = this;

				axios.get('https://api.twitch.tv/v5/videos/' + self.vidId + '/comments?' + query).then(ret => {
					let comments = ret.data.comments,
						next = ret.data._next;

					_.each(comments, function (comment) {

						self.messages.push({
							sender: comment.commenter.display_name,
							msg: comment.message.body
						});
					});

					if (next)
						self.nextMsg('cursor=' + next);
				});
			}
		}
	}
</script>