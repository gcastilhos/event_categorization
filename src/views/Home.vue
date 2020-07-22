<template>
  <div id="app">
    <div class="table">
    <div class="head">
        <div class="cell header big_font black">
        <h2>EVENT USE  CATEGORIZATION</h2>
        <h4>(Grouping and FILTERING as per USAGE.)</h4>
        </div>
        <cell-separator></cell-separator>
        <div class="cell big_font red">
        <span>EVENT HASHING</span>
        </div>
    </div>
     <div class="head">
        <div class="cell header dark_blue">
        <div v-for="item in header" :key="item" class="cell">
            {{ item }}
        </div>
        </div>
        <cell-separator></cell-separator>
        <cell-separator></cell-separator>
    </div>
    <div v-for="record in records" :key="record" class="row">
        <div class="cell record original dark_blue">
        <div v-for="(item, index) in record" :key="item" class="cell text mono" v-html="padding(item, index)">
        </div>
        </div>
        <cell-separator></cell-separator>
        <div class="cell record dark_blue">
        <div class="text hash">
            {{ record | recordAsString | encode }}
        </div>
        </div>
    </div>
    <div class="row">
        <cell-separator></cell-separator>
        <cell-separator></cell-separator>
        <div class="cell final_hash">
        <div class="row">
            <div class="cell no_lateral_padding">
            <div class="text light_blue note">
                <span>Block HASH for each 100 events</span>
            </div>
            </div>
        </div>
        <div class="row yellow">
            <div class="cell">
            <div class="text hash">
                {{ finalHash }}
            </div>
            </div>
        </div>
        </div>
    </div>
     </div>
  </div>
</template>

<script>
import CellSeparator from '@/components/CellSeparator.vue'

import sha256 from 'js-sha256';
import axios from 'axios';

var _encode = function(text) {
    if (text !== '') {
        return sha256(text);
    }
    return text;
};

export default {
        components: {
            CellSeparator
        },
        data: function() {
            return {
                records: [],
                header: [],
                finalHash: '',
                batch: 1,
                padSize: [6, 12, 18, 4, 15, 15, 10, 5, 6, 6, 5, 5, 5, 5]
                }
        },
        filters: {
            encode: _encode,
            recordAsString: function(record) {
                return record.join("|");
            },
        },
        methods: {
            padding: function(value, index) {
                var text = value;
                for (var i = 0; i < this.padSize[index] - value.toString().length; ++i) {
                    text = "&nbsp;" + text;
                }
                return text;
            },
            getData: function(batch) {
                axios.
                    get("./data/records_" + batch + ".json").
                    then(response => {
                        var data = response.data;
                        this.records.splice(data.data.length);
                        data.data.forEach((rec, index) => this.$set(this.records, index, rec.slice(1)));
                        this.header.splice(data.columns.length - 1);
                        this.header = data.columns.slice(1);
                        this.encodeAll();
                     });
            },
            encodeAll: function() {
                var finalHash = '';
                this.records.forEach((rec) => {
                    finalHash += _encode(rec);
                });
                this.finalHash = _encode(finalHash);
            },
        },
        mounted: function() {
            this.batch = this.$route.query.batch !== undefined ? this.$route.query.batch : 1;
            this.getData(this.batch);
        },
        created: function() {
            setInterval(function() {
                this.batch = this.batch === 100 ? 1 : this.batch + 1;
                this.getData(this.batch);
            }.bind(this), 5000);
        }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
