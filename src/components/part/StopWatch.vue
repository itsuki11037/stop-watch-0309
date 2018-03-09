<template lang='pug'>
.vue-stop-watch
    p.time.columns.is-mobile.is-centered.is-gapless
        span.column {{ String(hour).padStart(2, '0') }}
        span.column.is-narrow :
        span.column {{ String(min).padStart(2, '0') }}
        span.column.is-narrow :
        span.column {{ String(sec).padStart(2, '0') }}
        span.column.is-narrow :
        span.column {{ String(msec).padStart(2, '0') }}

    .columns.is-mobile.is-centered
        .column.is-narrow.alt
            button.button.is-primary.is-large(v-if='timerId == null' @click='start') START
            button.button.is-danger.is-large(v-if='timerId != null' @click='stop') STOP
        .column.is-narrow.alt
            button.button.is-warning.is-large(v-if='timerId == null' @click='reset') RESET
            button.button.is-info.is-large(v-if='timerId != null' @click='recordLaptime') LAPTIME

    p LAP-TIME
        .lap-time-box
            p.lap-times.columns.is-mobile.is-gapless(
                v-for="laptime, i in laptimes" :key='i')
                span.column.is-narrow {{ i + 1 }}
                span.column {{ }}
                span.column {{ String(laptime.hour) .padStart(2, '0') }}
                span.column.is-narrow :
                span.column {{ String(laptime.min) .padStart(2, '0') }}
                span.column.is-narrow :
                span.column {{ String(laptime.sec) .padStart(2, '0') }}
                span.column.is-narrow :
                span.column {{ String(laptime.msec) .padStart(2, '0') }}

</template>

<script lang='ts'>
import { Vue, Component } from 'vue-property-decorator';
import VueUtil from '@/scripts/util/VueUtil';
import { setInterval, clearInterval } from 'timers';

interface Time {
    msec: number;
    sec: number;
    min: number;
    hour: number;
}

/**
 * Vue Component
 */
@Component
export default class StopWatch extends Vue implements Time {
    private timerId: NodeJS.Timer | null = null;

    public msec: number = 0;
    public sec: number = 0;
    public min: number = 0;
    public hour: number = 0;

    private laptimes: Time[] = [];

    private start(): void {
        this.timerId = setInterval(this.countup, 10);
    }

    private stop(): void {
        if (this.timerId == null) {
            return;
        }
        clearInterval(this.timerId);
        this.timerId = null;
    }

    private reset(): void {
        this.timerId = null
        this.msec = 0;
        this.sec  = 0;
        this.min  = 0;
        this.hour = 0;
        this.laptimes = [];
    }

    private recordLaptime(): void {
        this.laptimes.push({
            hour: this.hour,
            min: this.min,
            sec: this.sec,
            msec: this.msec
        });
    }

    private countup():void {
        this.msec++;

        if (this.msec > 99) {
            this.msec = 0;
            this.sec++;
        }

        if (this.sec > 59) {
            this.sec = 0;
            this.min++;
        }

        if (this.min > 59) {
            this.min = 0;
            this.hour++;
        }
    }
}

</script>

<style lang='sass' scoped>
@import 'variable'

.vue-stop-watch
    max-width: 900px
    width: 80%
    margin: 0 auto

    .time
        margin: 15px

        & > span
            text-align: center
            font-size: 150px
    p
        font-size: $size-3

    .lap-time-box
         max-width: 500px
         max-height: 240px
         overflow: scroll
         margin: 0 auto

    .lap-times
         width: 81%
         text-align: center
         font-size: 50px
         margin: 0 auto

@media screen and (max-width: $tablet)
    .vue-stop-watch
        .time
            & > span
                font-size: 15vw


        .lap-times
            font-size: 7vw

        button
            font-size: 3vw
        p
            font-size: 5vw
</style>
