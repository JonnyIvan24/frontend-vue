<template>
  <div class="container">
    <div class="row text-left">
      <b-card no-body>
        <h4 slot="header">Agregar producto</h4>
        <b-card-body>
          <b-form @submit="addProduct" @reset="onReset">

            <div class="row">
              <b-form-group class="col-sm-2" id="input-group-1" label="Cantidad:" label-for="input-1">
                <b-form-input
                  id="input-1"
                  v-model.number="quantity"
                  type="number"
                  required
                ></b-form-input>
              </b-form-group>

              <b-form-group class="col-sm-6" id="input-group-2" label="Descripcion:" label-for="input-2">
                <b-form-input
                  id="input-2"
                  v-model.trim="description"
                  required
                ></b-form-input>
              </b-form-group>

              <b-form-group class="col-sm-2" id="input-group-3" label="Precio unitario:" label-for="input-3">
                <b-input-group prepend="$">
                  <b-form-input
                    id="input-3"
                    v-model.number="unitPrice"
                    type="number"
                    required
                  ></b-form-input>
                </b-input-group>
              </b-form-group>

              <b-form-group disabled  class="col-sm-2" id="input-group-4" label="Subtotal:" label-for="input-4">
                <b-input-group prepend="$">
                  <b-form-input
                    id="input-4"
                    v-model.number="subtotal"
                    disabled="disabled"
                    required
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
            </div>
            <div class="text-right">
              <b-button type="submit" variant="primary">Agregar</b-button>
              <b-button type="reset" variant="outline-secondary">Deshacer</b-button>
            </div>

          </b-form>
        </b-card-body>
        <br><br>
        <b-card-footer>
          <h4>Lista de productos</h4>
        </b-card-footer>
        <b-card-body>
          <b-card-text v-if="products.length === 0">
            No tiene productos agregados
          </b-card-text>
          <b-table v-else :fields="fields" :items="products" striped>
            <template slot="unitPrice" slot-scope="row">
              ${{row.item.unitPrice.toFixed(2)}}
            </template>
            <template slot="subtotal" slot-scope="row">
              ${{subtotalItem(row.item.quantity, row.item.unitPrice)}}
            </template>
            <template slot="actions" slot-scope="row">
              <b-button variant="success" @click="edit(row.item, row.index, $event.target)">Editar</b-button>
              <b-button variant="danger" @click="showMsgBoxTwo(row.item, row.index)">Eliminar</b-button>
            </template>
          </b-table>
        </b-card-body>
      </b-card>
    </div>
    <!-- Edit modal -->
    <b-modal :id="editModal.id" :ref="'modal'" size="xl" @ok="handleOk" @hide="resetEditModal">
      <template slot="modal-header">
        <h5>Editar producto</h5>
      </template>
      <b-form @submit.stop.prevent="addProduct" @reset="onReset">

        <div class="row">
          <b-form-group class="col-sm-2" id="input-group-1" label="Cantidad:" label-for="input-1">
            <b-form-input
              id="input-1"
              v-model.number="editModal.productEdit.quantity"
              type="number"
              required
            ></b-form-input>
          </b-form-group>

          <b-form-group class="col-sm-6" id="input-group-2" label="Descripcion:" label-for="input-2">
            <b-form-input
              id="input-2"
              v-model.trim="editModal.productEdit.description"
              required
            ></b-form-input>
          </b-form-group>

          <b-form-group class="col-sm-2" id="input-group-3" label="Precio unitario:" label-for="input-3">
            <b-input-group prepend="$">
              <b-form-input
                id="input-3"
                v-model.number="editModal.productEdit.unitPrice"
                type="number"
                required
              ></b-form-input>
            </b-input-group>
          </b-form-group>

          <b-form-group disabled  class="col-sm-2" id="input-group-4" label="Subtotal:" label-for="input-4">
            <b-input-group prepend="$">
              <b-form-input
                id="input-4"
                v-model="subtotalEdit"
                disabled="disabled"
                required
              ></b-form-input>
            </b-input-group>
          </b-form-group>
        </div>

      </b-form>
      <template slot="modal-footer" slot-scope="{ ok, cancel }">
        <b-button size="sm" variant="success" @click="ok()">Actualizar</b-button>
        <b-button size="sm" variant="secondary" @click="cancel()">Cancelar</b-button>
      </template>
    </b-modal>
  </div>
</template>

<script>
    export default {
      name: "Form",
      data(){
        return {
          disabled: true,
          quantity: '',
          description: '',
          unitPrice: '',
          fields:[
            {
              key: 'quantity',
              label: 'Cantidad'
            },
            {
              key: 'description',
              label: 'Descripción'
            },
            {
              key: 'unitPrice',
              label: 'Precio unitario'
            },
            {
              key: 'subtotal',
              label: 'Subtotal',
            },
            {
              key: 'actions',
              label: 'Acciones',
            },
          ],
          products: [],
          editModal: {
            id: 'edit-modal',
            title: 'Editar producto',
            index: '',
            productEdit: ''
          }
        }
      },
      computed:{
        subtotal: function () {
          var subtotal = this.quantity * this.unitPrice
          return subtotal.toFixed(2)
        },
        subtotalEdit: function () {
          var subtotal = this.editModal.productEdit.quantity * this.editModal.productEdit.unitPrice
          return subtotal.toFixed(2)
        }
      },
      methods:{
        addProduct(evt){
          evt.preventDefault()
          var request = {quantity: this.quantity, description: this.description, unitPrice: this.unitPrice, subtotal: this.subtotal}
          this.products.push(request)
          this.onReset()
        },
        onReset(){
          this.quantity = ''
          this.description = ''
          this.unitPrice = ''
        },
        edit(item, index, button) {
          // this.editModal.title = `Row index: ${index}`
          var productEdit = {quantity: item.quantity, description: item.description, unitPrice: item.unitPrice, subtotal: item.subtotal}
          this.editModal.productEdit = productEdit
          this.editModal.index = index
          this.$root.$emit('bv::show::modal', this.editModal.id, button)
        },
        handleOk(bvModalEvt) {
          // Prevent modal from closing
          bvModalEvt.preventDefault()
          // Trigger submit handler
          this.update()
        },
        update(){
          this.products.splice(this.editModal.index, 1, this.editModal.productEdit)
          // Hide the modal manually
          this.$nextTick(() => {
            this.$refs.modal.hide()
          })
        },
        resetEditModal() {
          this.editModal.title = ''
          this.editModal.productEdit = ''
          this.editModal.index = ''
        },
        showMsgBoxTwo(item, index) {
          this.$bvModal.msgBoxConfirm('¿Esta seguro de eliminar el producto '+item.description+'?', {
            title: 'Advertencia',
            size: 'sm',
            buttonSize: 'sm',
            okVariant: 'danger',
            okTitle: 'Sí',
            cancelTitle: 'No',
            footerClass: 'p-2',
            hideHeaderClose: false,
            centered: true
          })
            .then(value => {
              if (value){
                this.products.splice(index, 1)
              }
            })
            .catch(err => {
              // An error occurred
            })
        },
        subtotalItem(quantity, unitprice){
          var subtotal = quantity * unitprice
          return subtotal.toFixed(2)
        }
      }
    }
</script>

<style scoped>

</style>
