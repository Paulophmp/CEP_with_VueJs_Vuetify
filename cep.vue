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
                                        md6
                                >
                                    <v-text-field
                                            v-model="racaoSocial"
                                            :rules="racaoRules"
                                            :counter="50"
                                            label="Ração Social"
                                            required
                                    ></v-text-field>
                                </v-flex>

                                <v-flex
                                        xs12
                                        md6
                                >
                                    <v-text-field
                                            v-model="cnpj"
                                            :rules="cnpjRules"
                                            :mask="maskCnpj"
                                            :counter="14"
                                            label="CNPJ"
                                            required
                                    ></v-text-field>
                                </v-flex>
                            </v-layout>
                            <v-layout>
                                <v-flex
                                        xs12
                                        md6
                                >
                                    <v-combobox
                                            v-model="search"
                                            :items="items"
                                            label="Tipo de ambiente"
                                            chips
                                            :rules="tipoRules"
                                            required
                                    >
                                        <template
                                                slot="selection"
                                                slot-scope="data"
                                        >
                                            <v-chip
                                                    :selected="data.selected"
                                                    :disabled="data.disabled"
                                                    :key="JSON.stringify(data.item)"
                                                    class="v-chip--select-multi"
                                                    @input="data.parent.selectItem(data.item)"
                                            >
                                                <v-avatar
                                                        class="primary white--text"
                                                        v-text="data.item.slice(0, 2).toUpperCase()"
                                                />
                                                {{ data.item }}
                                            </v-chip>
                                        </template>
                                    </v-combobox>
                                </v-flex>
                            </v-layout>

                            <v-layout>
                                <v-flex
                                        xs12
                                        md4
                                >
                                    <v-text-field
                                            :counter="8"
                                            label="CEP"
                                            v-model="cep"
                                            :mask="mask"
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
                items: [
                    'Produção 1',
                    'Homologação 2',
                ],
                mask: '##.###-###',
                maskCnpj: '##.###.###/####-##',
                valid: true,
                racaoSocial: '',
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
                racaoRules: [
                    v => !!v || 'Ração Social is required',
                    v => v.length >= 5 || 'Name must be less than 5 characters'
                ],
                cnpjRules: [
                    v => !!v || 'Ração Social is required',
                    v => v.length >= 14 || 'Name must be less than 14 characters'
                ],
                cepRules: [
                    v => !!v || 'Cep is required',
                    v => v.length >= 8 || 'Cep must be less than  8 characters'
                ],
                tipoRules: [
                    v => !!v || 'Tipo de ambiente is required',
                ],
            }
        },
        created(){
            this.notasAction()
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
            resetValidation () {
                this.$refs.form.resetValidation()
            },
            searchCep () {
                if(this.cep != null && this.cep.length == 8) {
                    // axios.get(`https://viacep.com.br/ws/${ this.cep }/json/`)
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

<style>

</style>
<!--versão com o CEP via Axios-->