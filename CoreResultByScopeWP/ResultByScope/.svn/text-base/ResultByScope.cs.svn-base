using System;
using System.ComponentModel;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Web.UI.WebControls.WebParts;
using Microsoft.SharePoint;
using Microsoft.SharePoint.WebControls;
using Microsoft.Office.Server.Search.WebControls;
using ERREPAR.Utilidades;

namespace CoreResultByScopeWP.ResultByScope
{
    [ToolboxItemAttribute(false)]
    public class ResultByScope : CoreResultsWebPart
    {
        private string _Busca = "";
        [WebBrowsable(true)]
        [Category("Configuración")]
        [WebDisplayName("Busca, separar con punto y coma (;).\n\rEjemplo:'OR scope:\"EOL\"; OR scope:\"imn\"; OR scope:\"imn-mon\"'")]
        [Personalizable(PersonalizationScope.Shared)]
        public string Busca
        {
            get { return _Busca; }
            set { _Busca = value; }
        }

        private string _Reemplaza = "";
        [WebBrowsable(true)]
        [Category("Configuración")]
        [WebDisplayName("Reemplaza por, separar con punto y coma (;).\n\rEjemplo:'AND scope:\"EOL\"; AND scope:\"imn\"; AND scope:\"imn-mon\"'")]
        [Personalizable(PersonalizationScope.Shared)]
        public string Reemplaza
        {
            get { return _Reemplaza; }
            set { _Reemplaza = value; }
        }

        public bool _DisplayQuery = false;
        [WebBrowsable(true)]
        [Category("Configuración")]
        [WebDisplayName("Para ver o no el label con el contenido de AppendedQuery.")]
        [Personalizable(PersonalizationScope.Shared)]
        public bool DisplayQuery
        {
            get { return _DisplayQuery; }
            set { _DisplayQuery = value; }
        }

        private string _propiedadAdministrada = string.Empty;
        [WebBrowsable(true)]
        [Category("Configuración")]
        [WebDisplayName("Nombre de la propiedad administrada para el ordenamiento")]
        [Personalizable(PersonalizationScope.Shared)]
        public string PropiedadAdministrada { get { return _propiedadAdministrada; } set { _propiedadAdministrada = value; } }

        private Microsoft.Office.Server.Search.Query.SortDirection _SortDirection = Microsoft.Office.Server.Search.Query.SortDirection.Descending;
        [Personalizable(PersonalizationScope.Shared)]
        [WebBrowsable(true)]
        [WebDescription("Sort direction")]
        [WebDisplayName("Sort direction")]
        [Category("Configuración")]
        public Microsoft.Office.Server.Search.Query.SortDirection SortDirection
        {
            get { return _SortDirection; }
            set { _SortDirection = value; }
        }

        private string _rankingModelID = string.Empty;
        [WebBrowsable(true)]
        [Category("Configuración")]
        [WebDisplayName("GUID del Ranking Model a utilizar")]
        [Personalizable(PersonalizationScope.Shared)]
        public string RankingModelID { get { return _rankingModelID; } set { _rankingModelID = value; } }        

        const string AMBITOSDELUSUARIO = "AmbitosDelUsuario";

        protected override void OnInit(EventArgs e)
        {
            base.OnInit(e);
            try
            {
                if (Context.Session != null)
                {
                    string _permisos = string.Empty;

                    /* 1.-Tomo la variable de session   */
                    if (Context.Session[AMBITOSDELUSUARIO] == null)
                        Context.Session.Add(AMBITOSDELUSUARIO, Funciones.GetPermisos());

                    _permisos = Context.Session[AMBITOSDELUSUARIO].ToString();
                    // 1ra. vez llega con esto ==>> (scope:"Impuestos" OR scope:"Laboral" OR scope:"Para Sacar" OR scope:"EOL")  

                    /* 2.-saco los roles a excluir     */
                    //if (RolesAExcluir.Length != 0)
                    //{
                    //    string[] _colAExcluir = RolesAExcluir.Split(';');
                    //    foreach (string excluir in _colAExcluir)
                    //    {
                    //        if (excluir.Length != 0)
                    //            _permisos = _permisos.Replace(excluir, "");
                    //    }
                    //    ///tuvo que quedar con esto ==>> (scope:"Impuestos" OR scope:"Laboral")";
                    //}
                    if (Busca.Length != 0)
                    {
                        string[] _colBuscar = Busca.Split(';');
                        string[] _colReemplaza = Reemplaza.Split(';');
                        if (_colBuscar.Length == _colReemplaza.Length)
                        {
                            for (int i = 0; i < _colBuscar.Length; i++)
                            {
                                if (_colBuscar[i] != null)
                                {
                                    if (_colReemplaza[i] == null)
                                        _colReemplaza[i] = "";

                                    _permisos = _permisos.Replace(_colBuscar[i], _colReemplaza[i]);
                                }
                            }
                        }
                        else
                        {
                            throw new Exception("Los elementos en Busca difire de Reempla.");
                        }
                    }

                    /* 3.-armo el AppendedQuery con lo que quedó en _permisos y lo configurado en la misma propiedad (Anexar Texto a la consulta)  */
                    /// base.AppendedQuery tiene ==>> AND scope:"EOL"                         
                    base.AppendedQuery = _permisos + " " + base.AppendedQuery;

                    ///Resultado final con esto ==>> (scope:"Impuestos" OR scope:"Laboral") AND scope:"EOL"      
                }
            }
            catch (Exception ex)
            {
                Logger.Instance.Log(ex);
                Page.Response.Write("ERROR: " + ex.Message + "<br/><br/>" + ex.StackTrace);
            }
        }

        protected override void CreateChildControls()
        {
            base.CreateChildControls();
            // debug info
            // Representa la consulta del usuario al final de la webpart del coreResult
            string display = string.Empty;
            if (DisplayQuery)
                display = "block;";
            else
                display = "none;";

            Label debugInfo = new Label();
            debugInfo.ID = "ConsultaDelUsuario";
            debugInfo.Text = base.AppendedQuery;
            debugInfo.Attributes.Add("style", "display:" + display);

            Controls.Add(debugInfo);
        }

        protected override void ConfigureDataSourceProperties()
        {
            if (this.ShowSearchResults)
            {
                base.ConfigureDataSourceProperties();
                CoreResultsDatasource dataSource = this.DataSource as CoreResultsDatasource;
                if (!String.IsNullOrEmpty(this.PropiedadAdministrada))
                {
                    dataSource.SortOrder.Clear();                    
                    dataSource.SortOrder.Add(this.PropiedadAdministrada, this.SortDirection);
                }
                else if (!string.IsNullOrEmpty(this.RankingModelID))
                {
                    dataSource.RankingModelID = this.RankingModelID;
                }
            }

        }

    }
}
