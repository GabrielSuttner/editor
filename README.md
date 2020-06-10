# Editor
Currently this editor is not operational outside of a bigger project, it is not self contained. It is its own repository so that changes made to the editor can be easily shared between projects. 

# Layout Model
If there are updates to the model that is used for the layout, the model needs to be set here for continuity:

const mongoose = require('mongoose');

const layoutSchema = new mongoose.Schema({
  page: String,
  order: Number,
  title: String,
  subtitle: String,
  content: String,
  imgPath: String,
  template: String,
  publish: { type: Boolean, default: true },
  allowImage: Boolean,
  link: String,
  linkEnabled: Boolean,
});

const Layout = mongoose.model('Layout', layoutSchema);

module.exports = Layout;
