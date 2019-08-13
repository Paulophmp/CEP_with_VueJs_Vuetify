<template>
    <v-container>
        <div
                id="e3"
                style="max-width: 1200px; margin: auto;"
                class="grey lighten-3"
        >
            <v-toolbar
                    color="grey"
                    dark
            >
                <v-toolbar-title>Cadastar Certificado</v-toolbar-title>
                <v-spacer></v-spacer>
            </v-toolbar>
            <v-card>
                <v-container
                        fluid
                        grid-list-lg
                >
                    <v-form
                            ref="form"
                            v-model="valid"
                            lazy-validation
                    >
                        <v-container>
                            <v-layout>
                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            :counter="8"
                                            label="CEP"
                                            v-model="cep"
                                            :mask="maskCEP"
                                            :rules="cepRules"
                                            @keyup="searchCep"
                                            @keydown="searchCep($event)"
                                            required
                                    ></v-text-field>
                                </v-flex>
                            </v-layout>
                            <v-layout  v-if="cep != null && cep.length === 8">
                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            label="Logradouro"
                                            v-model="logradouro"
                                            box
                                            required
                                            clearable
                                    ></v-text-field>
                                </v-flex>
                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            label="Número"
                                            v-model="numero"
                                            ref="numeroCep"
                                            required
                                            box
                                            clearable
                                    ></v-text-field>
                                </v-flex>

                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            label="Complemento"
                                            required
                                            box
                                            v-model="complemento"
                                            clearable
                                    ></v-text-field>
                                </v-flex>
                            </v-layout>
                            <v-layout v-if="cep != null && cep.length === 8">
                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            label="Bairro"
                                            required
                                            box
                                            v-model="bairro"
                                            clearable
                                    ></v-text-field>
                                </v-flex>
                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            label="Cidade"
                                            required
                                            box
                                            v-model="localidade"
                                            clearable
                                    ></v-text-field>
                                </v-flex>
                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            :counter="2"
                                            label="UF"
                                            required
                                            v-model="uf"
                                            box
                                            clearable
                                    ></v-text-field>
                                </v-flex>
                            </v-layout>
                            <v-btn @click="submit">Cadastrar</v-btn>
                            <v-btn
                                    @click="resetValidation"
                            >
                                Limpar
                            </v-btn>
                        </v-container>
                    </v-form>
                </v-container>
            </v-card>
        </div>
    </v-container>
</template>

<script>
    import { mapActions, mapGetters } from 'vuex';

    export default {
        data(){
            return {
                maskCEP: '##.###-###',
                valid: true,
                cnpj: '',
                search: '',
                logradouro: '',
                complemento: '',
                bairro: '',
                localidade: '',
                numero: '',
                uf: '',
                cep : '',
                data : null,
                cepRules: [
                    v => !!v || 'Cep is required',
                    v => v.length >= 8 || 'Cep must be less than  8 characters'
                ],
            }
        },
        computed: {
            ...mapGetters({
                notasGetter: 'nota/notasGetter',
            })
        },
        methods: {
            ...mapActions({
                notasAction: 'nota/notasAction',
            }),
            submit () {
                if (this.$refs.form.validate()) {
                    this.snackbar = true
                }
            },
            searchCep () {
                if(this.cep != null && this.cep.length == 8) {
                    axios.get(`https://viacep.com.br/ws/${ this.cep }/json/`)
                        .then( response => {
                            this.showResults (response.data)
                        })
                        .catch( error => console.log(error) )
                }
            },
            showResults(address) {
                this.logradouro = address.logradouro;
                this.complemento = address.complemento;
                this.bairro = address.bairro;
                this.localidade = address.localidade;
                this.uf = address.uf;
                this.$refs.numeroCep.focus();
            }
        }
    }
</script>
