<template>
    <Page class="page">
        <ActionBar title="Audio" class="action-bar" />
        <ScrollView>
            <StackLayout class="home-panel">
                <Label :text="title" />
                <Progress :value="progress" />
                <Button class="btn btn-primary"
                    :text="isPlaying ? 'Pause' : 'Play'" @tap="playPause" />
                <ListView for="song in songs" style="height:1250px">
                    <v-template>
                        <FlexboxLayout flexDirection="row" class="list-entry">
                            <Label :text="song.artist"
                                v-on:tap="onItemTap(song)" class="t-14"
                                style="width: 40%" />
                            <Label :text="song.title"
                                v-on:tap="onItemTap(song)" class="t-12"
                                style="width: 60%" />
                        </FlexboxLayout>
                    </v-template>
                </ListView>
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
    const {
        isIOS
    } = require("tns-core-modules/platform");
    const {
        TNSPlayer
    } = require("../nativescript-audio");
    export default {
        data() {
            return {
                title: "Tap a song from the list",
                progress: 0,
                isPlaying: false,
                songs: [{
                        artist: "HAYDN",
                        title: "CONCERTO D-MAJOR",
                        src: "http://www.hochmuth.com/mp3/Haydn_Cello_Concerto_D-1.mp3"
                    },
                    {
                        artist: "TCHAIKOVSKY",
                        title: "ROCOCO-VARIATIONS",
                        src: "http://www.hochmuth.com/mp3/Tchaikovsky_Rococo_Var_orch.mp3"
                    },
                    {
                        artist: "VIVALDI SONATA",
                        title: "E-MINOR",
                        src: "http://www.hochmuth.com/mp3/Vivaldi_Sonata_eminor_.mp3"
                    },
                    {
                        artist: "TCHAIKOVSKY",
                        title: "NOCTURNE",
                        src: "http://www.hochmuth.com/mp3/Tchaikovsky_Nocturne__orch.mp3"
                    },
                    {
                        artist: "HAYDN",
                        title: "ADAGIO",
                        src: "http://www.hochmuth.com/mp3/Haydn_Adagio.mp3"
                    },
                    {
                        artist: "BOCCHERINI",
                        title: "CONCERTO IN D",
                        src: "http://www.hochmuth.com/mp3/Boccherini_Concerto_478-1.mp3"
                    },
                    {
                        artist: "BLOCH",
                        title: "PRAYER",
                        src: "http://www.hochmuth.com/mp3/Bloch_Prayer.mp3"
                    },
                    {
                        artist: "BEETHOVEN",
                        title: "VARIATIONS",
                        src: "http://www.hochmuth.com/mp3/Beethoven_12_Variation.mp3"
                    }
                ]
            };
        },
        mounted() {
            this._player = new TNSPlayer();

            this._checkInterval = setInterval(() => {
                this._player.getAudioTrackDuration().then(
                duration => {
                    // iOS: duration is in seconds
                    // Android: duration is in milliseconds
                    let current = this._player.currentTime;
                    if (isIOS) {
                        duration *= 1000;
                        current *= 1000;
                    }

                    this.progress = Math.ceil((current /
                        duration) * 100);

                    this.isPlaying = this._player
                        .isAudioPlaying();
                });
            }, 200);
        },
        destroyed() {
            clearInterval(this._checkInterval);
        },
        methods: {
            onItemTap: function(song) {
                console.log("Playing " + song.title + " by " + song
                    .artist);
                this.title = song.title + " by " + song.artist;
                const playerOptions = {
                    audioFile: song.src,
                    loop: false,
                    autoplay: false
                };
                this._player
                    .initFromUrl(playerOptions)
                    .then(res => {
                        console.log(res);
                    })
                    .catch(err => {
                        console.log("something went	wrong...", err);
                    });
            },

            playPause() {
                if (this._player.isAudioPlaying()) {
                    this._player.pause();
                } else {
                    this._player.play();
                }
            }
        }
    };
</script>

<style scoped>
    .list-entry {
        padding: 0 15;
        color: #42B883;
    }

    .list-entry Label {
        font-weight: bold;
        font-size: 17;
        vertical-align: middle;
        padding: 17 0;
        margin: 0;
    }
</style>