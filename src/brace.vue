<template>
  <div id="el-coding"></div>  
</template>
<script>
import * as brace from 'brace';
import 'brace/ext/modelist';
import 'brace/ext/themelist';

const regMap = {
    isInt: new RegExp('^\\d+$')
}

export default {
    data(){
        return {
            brace:null,
            modelist:brace.acequire('ace/ext/modelist'),
            themelist:brace.acequire('ace/ext/themelist')
        }
    },
    props:{
        mode: {
            type:String,
            default:'json',
            validator:(val) => this.modelist.modes.findIndex((mode) => mode.name === val) > -1
        },
        theme: {
            type:String,
            default:'github',
            validator:(val) => this.themelist.themes.findIndex((theme) => theme.name === val) > -1
        },
        fontSize:{
            type:String,
            default:'12px',
            validator:(val) => parseInt(val) > 11 && parseInt(val) < 25
        },
        codeFolding:{
            type:String,
            default:'markbegin',
            validator:(val) => ['manual','markbegin','markbeginend'].includes(val)
        },
        softWrap:{
            type:String,
            default:'free',
            validator:(val) => (['off','free'].includes(val) || regMap.isInt.test(val))
        },
        selectionStyle:{
            type:String,
            default:'text',
            validator:(val) => ['text','line'].includes(val)
        },
        highlightLine:{
            type:Boolean,
            default:true
        }
    },
    watch:{
        mode(){
            this.setMode();
        },
        theme(){
            this.setTheme();
        },
        fontSize(val){
            this.brace.setFontSzie(val);
        },
        codeFolding(val){
            this.brace.session.setFoldStyle(val);
            this.brace.setShowFoldWidgets(val !== 'manual')
        },
        softWrap(val){
            this.brace.setOption('wrap',val);
        },
        selectionStyle(val){
            this.brace.setOption('selectionStyle',val);
        },
        highlightLine(val){
            this.brace.setHighlightActiveLine(val);
        }
    },
    methods:{
        setMode(){
            let mode = this.modelist.modesByName[this.mode];

            if (mode) {
                require('brace/mode/' + mode.name);
                this.brace.getSession().setMode(mode.mode);
            }
        },
        setTheme(){
            let theme = this.themelist.themesByName[this.theme];

            if (theme) {
                require('brace/theme/' + theme.name);
                this.brace.setTheme(theme.theme);
            }
        },
        emitCode(){
            this.$emit('code-change',this.brace.getValue());
        }
    },
    mounted(){
        this.brace = brace.edit('el-editor');
        this.setMode();
        this.setTheme();
        this.brace.$blockScrolling = Infinity;
        this.brace.getSession().on('change',this.emitCode);
    }
}
</script>

